---
title: Hozzon létre egy gépi tanulási megoldásokat az Azure és az Azure Stack megoldás |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre a Python használata Azure-ban és az Azure Stack edge machine learning megoldásokat.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: c195f2ee24b61a57c098d5214a37f65e80845074
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410204"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>Oktatóanyag: Hozzon létre egy gépi tanulási megoldásokat az Azure és az Azure Stack megoldás

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Ismerje meg, hogyan hozhat létre egy gépi tanulási megoldásokat az Azure és az Azure Stack megoldás.

Ebben az oktatóanyagban egy mintául szolgáló környezet fog létrehozni:

> [!div class="checklist"]
> - Hozzon létre egy tárfiókot és egy tárolót a tiszta adatok tárolását.
> - Hozzon létre egy Ubuntu adatelemzési virtuális gép (DSVM) az Azure Portalon.
> - Telepítse az Azure Machine Learning-szolgáltatások az Azure-ban hozhat létre és-modellek betanításához.
> - Az Azure Machine Learning services fiókokat hozhat létre.
> - Telepítéséhez és használatához az Azure Container Registrybe.
> - Kubernetes-fürt üzembe helyezése az Azure Stackhez.
> - Hozzon létre egy Azure Stack tárfiókok és a Storage-üzenetsor adatokat.
> - Hozzon létre egy új Azure Stack-függvényt, amely a tiszta adatok áthelyezése az Azure Stack az Azure-bA.

**Ez a megoldás használata**

 -  A szervezet használja a fejlesztési és üzemeltetési megközelítést, vagy rendelkezik egy tervezett a közeljövőben.
 -  Szeretné végrehajtani a CI/CD-eljárások az Azure Stack végrehajtása és a nyilvános felhőben.
 -  Szeretne felhőbeli és helyszíni környezetek között a CI/CD-folyamat egyesíthetők.
 -  Szeretné állítani, felhőbeli vagy helyszíni szolgáltatásokat használó alkalmazások fejlesztéséhez.
 -  Érdemes egységes fejlesztési ismeretekkel, felhőalapú és helyszíni alkalmazásokban.

> [!Tip]  
> ![hibrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> A Microsoft Azure Stack az Azure bővítménye. Az Azure Stack hatékonyságával és innovációjával a felhőalapú számítástechnika a helyszíni környezetben, és a egyetlen olyan hibrid felhő, amely lehetővé teszi, hogy létrehozása és üzembe helyezése bárhol a hibrid alkalmazások engedélyezésével biztosítható.  
> 
> A tanulmány [hibrid alkalmazások kapcsolatos kialakítási szempontok](https://aka.ms/hybrid-cloud-applications-pillars) szoftverminőség alappillérei (elhelyezését, a méretezhetőség, rugalmasság, kezelhetőségi és biztonsági) felülvizsgálatai kialakítása, üzembe helyezése és működtetése hibrid alkalmazások. A kialakítási szempontokat segítséget nyújt a hibrid alkalmazások kialakítását, minimálisra csökkentik az éles környezetben kihívások optimalizálása.

## <a name="prerequisites"></a>Előfeltételek

Néhány összetevők ezt a használati esetet létrehozásához szükséges, és eltarthat egy ideig előkészítése:

 -  Az OEM-hardver partnere olyan éles környezetben az Azure Stack helyezheti üzembe, és minden felhasználó egy ASDK helyezheti üzembe

 -  Az Azure Stack-operátorokról kell is az App Service üzembe helyezése, csomag és ajánlat létrehozása, hozzon létre egy bérlői előfizetést és a Windows Server 2016-lemezképének hozzáadása

 -  A hibrid hálózat és az App Service kell beállítani (További információ [virtuális hálózatokhoz való integrálásáról.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  A Magánfelhő [Buildelési és kiadási ügynök](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) a VSTS-integrációval telepítené (bármely meglévő összetevőket használja, gondoskodjon arról, hogy teljesülnek az megkezdése előtt.) kell állítani

Az Azure és az Azure Stack előzetes ismeretek megadása kötelező. További a folytatás előtt indítsa el a következő témakörökben:

 -  [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Az Azure Stack főbb fogalmak](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Az Azure Stack hibrid CI/CD a megoldási útmutató](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  Azure-előfizetés (hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  Új webes alkalmazás URL-cím által létrehozott [webalkalmazás](https://docs.microsoft.com/azure/app-service/app-service-web-overview) az Azure-ban

 -  Az üzembe helyezési [az Azure Container Services, Azure-beli Kubernetesben (ACS)](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Az Azure Machine Learning szolgáltatás (előzetes verzió) üzembe helyezési [4 részes oktatóanyag](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)

 -  Az Azure Machine Learning-kísérletezés [fiók](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template)

**Azure Stack**

 -  Az Azure Stackkel integrált rendszereknél vagy az Azure Stack fejlesztői készletének telepítése.

    - Látja a történő telepítése, Azure Stack [az Azure Stack fejlesztői készletének telepítése](/articles/azure-stack/asdk/asdk-install).
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Ez a telepítés néhány órát lehet szükség.

 -  Az üzembe helyezési [App Service-ben](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-szolgáltatások az Azure Stackhez

 -  A [csomag/ajánlatok](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) belül az Azure Stack-környezet

 -  A [bérlői előfizetéshez](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) belül az Azure Stack-környezet

 -  Egy Ubuntu Server Virtuálisgép-lemezkép (elérhető [Azure Stack piactéren](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)), a saját build ügynök, valamint a Kubernetes-beli virtuális gépek az Azure Stack bérlő előfizetésben található virtuális gép lesz felépítve. Ez a rendszerkép nem érhető el, ha az Azure Stack-operátorokról végrehajtásában, annak érdekében, hogy a rendszer felveszi a környezetbe. (Ne használja a 18.04 build az ubuntu, mert jelenleg nem támogatott.)

 -  A Web App (Megjegyzés: az új webes alkalmazás URL-címe későbbi használatra.) bérlő az előfizetésen belül

 -  Üzembe helyezés a VSTS Linux-alapú privát hozhat létre az ügynök virtuális gép, a bérlő az előfizetésen belül

 -  Üzembe helyezés egy [az Azure Container Services-(ACS) Kubernetes az Azure Stackben](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**Fejlesztői eszközök**

 -  [VSTS-munkaterület](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (a regisztrációs folyamat hoz létre egy "MyFirstProject" nevű projekt)

 -  [A Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) telepítési és [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) bejelentkezés

 -  [Helyi klón](https://www.visualstudio.com/docs/git/gitquickstart) projekt

 -  [A Windows 10 Linux alrendszer](https://docs.microsoft.com/windows/wsl/install-win10) (a BASH és SSH) telepítése

 -  [A Windows docker](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) telepítése

 -  [Az Azure Machine Learning Workbench (előzetes verzió)](https://aka.ms/azureml-wb-msi) telepítése

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) környezet telepítése

**VSTS**

 -  **A VSTS-fiók.** Gyorsan buildelési, tesztelési és üzembe helyezés a folyamatos integráció beállítása. VSTS-fiókokkal kapcsolatos további információkért lásd: [a VSTS-fiók létrehozása](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts).

 -  **A kódtár.** A meglévő kódtárházakhoz, a GitHub, BitBucket, DropBox, Onedrive és a TFS, a VSTS-platform használhatják a több kódtárházakhoz, a fejlesztési folyamat leegyszerűsíthető. Kód további információ: tárházak [Ismerkedés a Git és a VSTS](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) oktatóanyag.

 -  **Kapcsolat a szolgáltatással.** Csatlakozhat a külső és a távoli szolgáltatás tesztelése, a build és a központi telepítési feladatok végrehajtásához. További információ a VSTS szolgáltatáskapcsolatokat: [a Build és kiadás Szolgáltatásvégpontok](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts).

 -  **Definíciókat hozhat létre.** Automatizált összeállítási folyamatairól határozza meg, és a compose feladatokhoz a feladat-katalógus használatával. További információ build definíciók: [Builddefiníciót hozzon létre](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentációját.

 -  **Kiadási definícióiról.** Adja meg a telepítési folyamat egy gyűjtemény környezetek, az alkalmazás-összetevők telepítve vannak. További információ a kiadással kapcsolatos definíciókat: [kiadási definíció létrehozása](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentációját.

 -  **Vsts-ben üzemeltetett Linux Ügynökkészlet hozhat létre.** Gyorsan készíthet, tesztelhet és helyezhet üzembe alkalmazásokat használatával egy Microsoft által felügyelt és a fenntartott futó ügynök. További információ a vsts-ben üzemeltetett buildelési ügynökök lásd [üzemeltetett ügynökök](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts) dokumentációját.

## <a name="step-1-create-a-storage-account"></a>1. lépés: Tárfiók létrehozása

Hozzon létre egy tárfiókot és egy tárolót a tiszta adatok tárolását.

1.  Jelentkezzen be a [ *az Azure portal*](https://portal.azure.com/).

2.  Az Azure Portalon bontsa ki a szolgáltatások a menü megnyitásához, majd válassza a bal oldali **minden szolgáltatás**. Görgessen le a **tárolási** válassza **tárfiókok**. Az a ** Tárfiókok ** ablakban válassza a **Hozzáadás**.

3.  Adja meg a tárfiók nevét.

    > [!Note]  
    > A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. A tárfiók nevének Azure-on belül egyedinek kell lennie. Az Azure portal jelzi, ha a kiválasztott tárfiók neve már használatban van.

4.  Adja meg a használt üzemi modell: **Resource Manager**.

5.  Válassza ki a tárfiók típusát: **általános célú V1**, majd adja meg a teljesítményszintet: **Standard**.

6.  Válassza ki a tárfiók replikálási beállítását: **GRS**.

7.  Válassza ki az új tárfiók-előfizetés.

8.  Adjon meg egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.

9.  Válassza ki a tárfiók földrajzi helyét.

10. Kattintson a **Létrehozás** gombra a tárfiók létrehozásához.

    ![Helyettesítő szöveg](\media\azure-stack-solution-machine-learning\image1.png)

11.  Válassza ki a nemrég létrehozott tárfiókot.

12.  Válassza ki a **Blobok**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image2.png)

13.  Válassza ki a **+ tároló** , és válassza ki a **tároló**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image3.png)

14.  Nevezze el a tároló a **uploadeddata** , és válassza ki a hozzáférési **tároló**.

15.  Válassza ki a **létrehozása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>2. lépés: Hozzon létre egy Data Science virtuális gép

Hozzon létre egy Ubuntu adatelemzési virtuális gép (DSVM) az Azure Portalon.

1.  Jelentkezzen be az Azure Portalon [https://portal.azure.com](https://portal.azure.com/)

2.  Válassza ki a a **+ új** hivatkozásra, és keresse meg a "Data Science virtuális gép a Linux Ubuntu CSP

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image5.png)

1.  Válassza a **adatelemző virtuális gép Linux (Ubuntu)** a listában, és kövesse a képernyőn megjelenő utasításokat követve hozzon létre a dsvm-hez.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image6.png)

> ! [Fontos]  
> **Válasszon** jelszó **, a*hitelesítési típus*.

Helyezze el az új dsvm-hez az újonnan létrehozott tárfiók ugyanabban az erőforráscsoportban. Minden Edge Machine Learning objektumot az Azure-ban az erőforráscsoporton belül vannak telepítve.

1.  A választható szolgáltatások beállítások konfigurálása

    a.  Válassza ki a **Tárfiók** korábban létrehozott.

    b.  Hozzon létre egy új **virtuális hálózat**, **alhálózati**, és **nyilvános IP-cím** , alapértelmezés szerint hozzon létre egy nevet az erőforráscsoport neve alapján.

    c.  Hozzon létre egy új **NSG** , érdemes hozza létre automatikusan a már telepített megfelelő szabályokat.

    d.  Az a **diagnosztikai Tárfiók**, válassza ki a korábban létrehozott tárfiókot.

    e.  Megjegyzés: Az aad-ben engedélyezni és konfigurálni az Azure-előfizetéshez tartozó, a Felügyeltszolgáltatás-identitást engedélyezhető is.

2.  Kattintson az **OK** gombra.

### <a name="connect-to-the-dsvm"></a>A dsvm-hez való csatlakozáshoz

A DSVM létrehozása után csatlakozhat a virtuális gép a Windows alrendszer Linux rendszeren.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  Igen, ha a virtuális gép kapcsolatának megerősítéséhez írja be.

2.  Adja meg a jelszót a dsvm-hez készült.

### <a name="update-the-dsvm"></a>Frissítés a dsvm-hez 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>3. lépés: Az Azure Machine Learning-szolgáltatások üzembe helyezése

Telepítse az Azure Machine Learning-szolgáltatások az Azure-ban.

Az Azure Machine Learning-szolgáltatások (előzetes verzió) átfogó, integrált és fejlett adatelemzési megoldások. A hivatásos adatszakértők számára nyújt segítséget az adatok előkészítésében, a kísérletek kidolgozásában és a modellek felhőszinten való üzembe helyezésében.

Ez a szakasz ismerteti:

> [!div class="checklist"]
> - Szolgáltatásfiókok létrehozása az Azure Machine Learning-szolgáltatásokhoz
> - Telepítse az Azure Machine Learning Workbenchet, és jelentkezzen be.
> - Projekt létrehozása a Workbenchben
> - Szkriptek futtatása a projektben
> - A parancssori felület (CLI) elérése

Mivel a Microsoft Azure-portfólió részét képezik, az Azure Machine Learning-szolgáltatások használatához szükség van egy Azure-előfizetésre. Szerezze be az Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Megfelelő engedélyekkel az erőforráscsoportok, virtuális gépek és egyéb eszközök hasonló objektumok létrehozásához szükségesek.

Az Azure Machine Learning Workbench alkalmazást a következő operációs rendszereken telepíthető:

 -  Windows 10 vagy Windows Server 2016
 -  macOS Sierra vagy High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>4. lépés: Az Azure Machine Learning-szolgáltatások létrehozásához

Az Azure Machine Learning services fiókokat hozhat létre.

Az Azure portal használatával üzembe helyezése az Azure Machine Learning-fiókokat:

1.  Jelentkezzen be a [az Azure portal](https://portal.azure.com/) használható az Azure-előfizetés hitelesítő adataival. Szerezze be az Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image7.png)

1.  A portál bal felső sarkában válassza az **Erőforrás létrehozása** (+) gombot.

    ![Erőforrás létrehozása az Azure Portalon](media\azure-stack-solution-machine-learning\image8.png)

1.  Írja be a **Machine Learning** kifejezést a keresősávba. Válassza a **Machine Learning-kísérletezés (előzetes verzió)** elnevezésű keresési eredményt.

    ![Azure Machine Learning-keresés](media\azure-stack-solution-machine-learning\image9.png)

1.  Az a **Machine Learning-kísérletezés** panelen görgessen lefelé, és válassza **létrehozás** , a Kísérletezési fiók meghatározásának megkezdéséhez.

    ![Azure Machine Learning – kísérletezési fiók létrehozása](media\azure-stack-solution-machine-learning\image10.png)

1.  Az a **ML-kísérletezés** panelen konfigurálja a Machine Learning-kísérletezés-fiókot.

    | Beállítás | Oktatóanyaghoz ajánlott érték | Leírás |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Kísérletezés-fiók neve | Egyedi név | Adjon meg egy egyedi nevet a fiók azonosításához. Használjon egy részleg vagy projekt nevét, amely a legjobban azonosítható a kísérlet. A név 2–32 karakter hosszúságú lehet. A név csak alfanumerikus és kötőjel (-) karaktert tartalmazhat. |
    | Előfizetés | Az előfizetés | Válassza ki az Azure-előfizetés a kísérlethez használni. Ha több előfizetést is létezik, válassza ki a megfelelő előfizetést, amelyen az erőforrás terhelve van. |
    | Erőforráscsoport | Az erőforráscsoport | Használjon egy meglévő erőforráscsoportot az előfizetésben, vagy adjon meg egy nevet a Kísérletezési fiókhoz tartozó új erőforráscsoport létrehozásához. |
    | Hely | A felhasználókhoz legközelebb eső régió | Válassza ki a felhasználók és az adatforrásokhoz legközelebb eső helyet. |
    | Munkaállomások száma | 2 | Adja meg a munkaállomások számát. Itt találhat további tudnivalókat arról, [hogyan befolyásolja a munkaállomások száma a díjszabást](https://azure.microsoft.com/pricing/details/machine-learning/).<br><br>Ebben a rövid útmutatóban csak két munkaállomás van szükség. A munkaállomások igény szerint adhatók hozzá és távolíthatók el az Azure Portalon. |
    | Tárfiók | Egyedi név | Válassza az **Új létrehozása** elemet, és adjon meg egy nevet az [Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal) létrehozásához. A név 3–24 karakter hosszúságú lehet, és csak alfanumerikus karaktereket tartalmazhat. Jelölje ki **meglévő** , és válassza ki a meglévő tárfiókot a listából. A tárfiók megadása kötelező, hiszen a projektösszetevőket és a futtatási előzményadatokat tárolja. |
    | A Kísérletezés-fiók munkaterülete | IrisGarden<br>(az oktatóanyagokban használt név) | Adja meg a fiók munkaterületének nevét. A név 2–32 karakter hosszúságú lehet. A név csak alfanumerikus és kötőjel (-) karaktert tartalmazhat. Ez a munkaterület létrehozásához, kísérletek kezeléséhez és közzétételéhez szükséges eszközöket tartalmazza. |
    | A munkaterülethez társított tulajdonos | A fiók | Válassza ki a saját fiókot a munkaterület tulajdonosaként. |
    | Modellkezelési fiók létrehozása | **bejelölve** | Hozzon létre egy Modellkezelési fiókot. Ez lesz üzembe helyezése és kezelése a modellek valós idejű webszolgáltatásként. <br><br>Ez nem kötelező, ajánlott a Modellkezelési fiók létrehozásához a Kísérletezési fiókkal egy időben. |
    | Fióknév | Egyedi név | Válasszon egy egyedi nevet a Modellkezelési fiók azonosításához. Használja a részlegek vagy projekt nevét, amely a legjobban azonosítható a kísérlet. A név 2–32 karakter hosszúságú lehet. A név csak alfanumerikus és kötőjel (-) karaktert tartalmazhat. |
    | Modellkezelési tarifacsomag | **DEVTEST** | Válassza ki **nincs kijelölt tarifacsomag** a Modellkezelés-fiók tarifacsomagjának megadásához. Költségmegtakarítás érdekében válassza a DEVTEST tarifacsomagot, ha elérhető az előfizetésén (korlátozottan elérhető). Ellenkező esetben válassza az S1 tarifacsomagot. Válassza ki a tarifacsomag beállításának mentéséhez válassza. |
    | Rögzítés az irányítópulton | Jelölőnégyzet | Válassza ki a **rögzítés az irányítópulton** lehetőség kiválasztásával egyszerűen nyomon követheti a Machine Learning-kísérletezés fiókját az Azure Portal irányítópultjának első lapján. |

    ![A Machine Learning-kísérletezési fiók konfigurálása](media\azure-stack-solution-machine-learning\image11.png)

1.  A **Létrehozás** lehetőség kiválasztásával indíthatja el a kísérletezési fiók és a modellkezelési fiók létrehozási folyamatát.

    ![A Machine Learning-kísérletezési fiók konfigurálása](media\azure-stack-solution-machine-learning\image12.png)

    Hozzon létre egy fiókot egy kis ideig is eltarthat. Az Azure portal eszköztárában található értesítések ikonra (harang) kiválasztásával az üzembehelyezési folyamat állapotának ellenőrzéséhez.

    ![Az Azure Portal értesítései](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>Telepítse, és jelentkezzen be a workbench 

Az Azure Machine Learning Workbench Windows és macOS rendszeren érhető el. Itt megtalálhatja a [támogatott platformok](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation) listáját.

**Figyelmeztetés:** befejeződik a telepítés eltarthat egy órát.

1.  Töltse le és indítsa el a Workbench legújabb verziójának telepítőjét.

    > [!Important]  
    > Töltse le a telepítőt a meghajtóra, majd futtassa onnan. Ne futtassa ezt a böngésző letöltésvezérlőjéből közvetlenül a.<br>**A Windows:<br>**  egy. Töltse le az [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi) fájlt.<br>b. Kattintson duplán a letöltött telepítőre a Fájlkezelőben.

1.  Kövesse a képernyőn megjelenő utasításokat a telepítő befejezését.

    ** A telepítést, akár 30 percet is igénybe vehet. **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    A telepítő letölti és beállítása az összes szükséges függőséget, mint például a Python, a Miniconda és egyéb kapcsolódó kódtárakat. Ez a telepítés is tartalmaz az Azure többplatformos parancssori eszközt, vagy az Azure CLI.

1.  A Workbench elindításához válassza a **Workbench indítása** gombot a telepítő utolsó lapján.

    Ha a telepítő le van zárva, indítsa el a használatával a **Machine Learning Workbench** asztali parancsikonjára.

1.  Válassza ki **Bejelentkezés Microsoft-fiókkal** az Azure Machine Learning Workbenchben való hitelesítéshez. A kísérleti és Modellkezelési fiókok létrehozása az Azure Portalon használt ugyanazon hitelesítő adatokat használja.

    Miután bejelentkezett, a Workbench, az Azure-előfizetések talál, és megjeleníti az összes munkaterületeket és projekteket a fiókhoz társított első Kísérletezési fiókot használja.

    > [!Tip]  
    > Váltson egy másik Kísérletezési fiókra a ikonra a Workbench alkalmazás ablakának bal alsó sarkában.

### <a name="create-a-new-project-in-workbench"></a>Új projekt létrehozása a workbenchben

1.  Nyissa meg az Azure Machine Learning Workbench alkalmazást, és jelentkezzen be, ha szükséges.

    - A Windows, indítsa el a használatával a **Machine Learning Workbench** asztali parancsikonjára.

    - MacOS rendszeren válassza a Launchpaden az **Azure ML Workbench** elemet.

1.  Válassza a **PROJEKTEK** ablaktáblán a plusz jelet (+), majd az **Új projekt** lehetőséget.

    ![Új munkaterület](media\azure-stack-solution-machine-learning\image14.png)

1.  Töltse ki az űrlap mezőit, és válassza a **Létrehozás** gombot, hogy új projektet hozzon létre a Workbenchben.

    | Mező | Oktatóanyaghoz ajánlott érték | Leírás |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Projektnév | myIris | Adjon meg egy egyedi nevet a fiók azonosításához. Használja a részlegek vagy projekt nevét, amely a legjobban azonosítható a kísérlet. A név 2–32 karakter hosszúságú lehet. A név csak alfanumerikus és kötőjel (-) karaktert tartalmazhat. |
    | Projektkönyvtár | c:\Temp\ | Adja meg a könyvtárat, amelyben a projekt létrejött. |
    | Projekt leírása | Hagyja üresen | A projekt leírására szolgáló mező, amelyet nem kötelező kitölteni. |
    | Visualstudio.com GIT-adattár URL-címe | Hagyja üresen | Nem kötelező kitölteni. Társítson egy projektet egy Git-tárház a Visual Studio Team Services a forráskezelés és az együttműködés. [Ismerje meg, hogyan állítható be egy tárház](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project). |
    | Kiválasztott munkaterület | IrisGarden (ha van) | Válassza ki egy munkaterület a Kísérletezési fiók az Azure Portalon létrehozott. <br>Rövid útmutató segítségével, a munkaterület IrisGarden nevű szerepel. Ellenkező esetben használja a munkaterület a Kísérletezési fiók neve, vagy egy előnyben részesített fiók nevét. |
    | Projektsablon | Írisz osztályozása | Sablonok szkripteket és Fedezze fel a termék segítségével adatokat tartalmaznak. Ez a sablon tartalmazza a szkripteket és a dokumentációs webhelyen található ebben a rövid és a többi szükséges adatokat. |

    ![Új projekt](media\azure-stack-solution-machine-learning\image15.png)

1.  Létrejön egy új projekt, és megnyílik az irányítópultja. Fedezze fel a projekt kezdőlapját, adatforrások, jegyzetfüzeteket és forráskódfájljait.

    ![Projekt megnyitása](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>A DSVM számítási célnak csatolása

A DSVM-létrehozása után csatlakoztassa azt az Azure Machine Learning-projektet.

1.  A az Azure Machine Learning Workbench alkalmazásban, indítsa el az Azure Machine Learning Workbench parancssori felület kiválasztásával **fájl**->**megnyitott PowerShell**

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image17.png)

1.  Ha a PowerShell-parancssort nyitotta meg a következő paranccsal:

    ```PowerShell  
        az login
    ```

1.  A következő üzenet:

     ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image18.png)

1.  Tallózással keresse meg a helyet, a rendszer kéri a, és adja meg a kódot, amely biztosítja.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image19.png)

1.  Kattintson a Folytatás, amikor a rendszer kéri, majd válassza ki az Azure-fiók az Azure Machine Learning kísérleti fiók hozzá van rendelve.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image20.png)

1.  Az Azure Machine Learning Workbench parancssori felület ezután elküldi a következő üzenet:

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image21.png)

1.  ML-fiók és a munkaterület bejelentkezés sikeres jelenik meg, amikor csatolni a dsvm-hez.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    A következő értesítés jelenik meg:

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

Ez a folyamat hosszabb időt vesz igénybe, és a szöveg jelentős mennyiségű hoz létre, különböző docker-rendszerképek lekéri, regisztrálja őket, és majd alkalmazza a szükséges kódot és az alkalmazások őket.

Kísérletek most futtatni a a dsvm-hez.

### <a name="create-a-data-preparation-package"></a>Adat-előkészítési csomag létrehozása

Ezután indítsa el az előkészítésüket az Azure Machine Learning Workbench alkalmazásban. A Workbenchben végzett minden átalakítás JSON formátumban egy helyi adatelőkészítési csomagban tárolja (\*.dprep fájlt). Az adatelőkészítési csomag a Workbench az adatok előkészítési munkahelyi elsődleges tárolója.

Az adatelőkészítési csomag átadható később egy futtatókörnyezetnek, például a local-C\#/CoreCLR, a Scala/Sparknak vagy a Scala/hdi-nek.

1.  Válassza ki a mappaikont a Fájlok nézet megnyitásához, majd az **iris.csv** fájlt a tartalma megjelenítéséhez.

    A fájl egy 5 oszlopból és 50 sorból álló táblázatot tartalmaz. Négy oszlop számokat tartalmaz olyan. Az ötödik oszlop a karakterláncokat. Egyik oszlopnak sincs fejlécneve.

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  Az **Adatnézetben** válassza a pluszjelet (**+**) egy új adatforrás hozzáadásához. Megnyílik az **Adatforrás hozzáadása** lap.

    ![Az Adatnézet az Azure Machine Learning Workbenchben](media\azure-stack-solution-machine-learning\image24.png)

1.  Válassza ki **szövegfájlok (\*.csv, \*.json, \*.txt., …)** .

    ![Az adatforrás az Azure Machine Learning Workbenchben](media\azure-stack-solution-machine-learning\image25.png)

1.  Kattintson a **Tovább** gombra.

2.  Keresse meg a fájlt **iris.csv**, és válassza ki **Befejezés**. Így a paraméterek (például az elválasztó- és adattípusok) alapértelmezett értékei lesznek használatban.

    > [!Important]  
    > Válassza ki a **iris.csv** ehhez a gyakorlathoz az aktuális projektmappából fájlt. Ellenkező esetben későbbi lépések sikertelenek lehetnek.

    ![Válassza ki az iris elemet](media\azure-stack-solution-machine-learning\image26.png)

1.  Egy új fájlt `*iris-1.dsource` jön létre. A fájl neve egyedi `-1` , mert a projektben már van egy nem számozott **iris.dsource** fájlt.

    Megnyílik a fájl, és megjelennek az adatok. Egy oszlopfejlécet a **Column1** való **Column5**, ez az adatkészlet automatikusan hozzáadódik. Görgessen le, és figyelje meg, hogy az adatkészlet utolsó sora üres. A sor a CSV-fájl az extra sortörést miatt je prázdná.

    ![Iris adatnézet](media\azure-stack-solution-machine-learning\image27.png)

1.  Válassza a **Mérőszámok** gombot. Hisztogramok jönnek létre és jelennek meg.

    Váltson vissza az adatnézetre kiválasztásával a **adatok** gombra.

    ![Iris adatnézet](media\azure-stack-solution-machine-learning\image28.png)

1.  Tekintse át a hisztogramokat. A rendszer minden oszlophoz részletes statisztikákat számított ki.

    ![Iris adatnézet](media\azure-stack-solution-machine-learning\image29.png)

1.  Az **Előkészítés** gombra kattintva kezdheti el az adatelőkészítési csomag létrehozását. Megnyílik az **Előkészítés** párbeszédpanel.

    A mintaprojekt tartalmaz egy **iris.dprep** adatelőkészítési fájlt, amely alapértelmezés szerint ki van választva.

    ![Iris adatnézet](media\azure-stack-solution-machine-learning\image30.png)

1.  Hozzon létre egy új adat-előkészítési csomag kiválasztásával **+ új adatelőkészítési csomag** a menüből.

    ![Iris adatnézet](media\azure-stack-solution-machine-learning\image31.png)

1.  Adjon meg egy új értéket a csomag neveként (**iris-1**), majd válassza az **OK** gombot.

    Egy új adat-előkészítési csomag **iris-1.dprep** létrejön és megnyílik az adatelőkészítés-szerkesztőben.

    ![Iris adatnézet](media\azure-stack-solution-machine-learning\image32.png)

    Ezután az adat-előkészítési van szükség.

1.  Ehhez jelölje ki egyenként az oszlopfejléceket, és tegye szerkeszthetővé a fejlécszöveget. Ezután nevezze át az oszlopokat a következőképpen:

    Adja meg sorrendben **csészelevél hossza**, **csészelevél szélessége**, **szirom hosszúsága**, **szirom szélessége**, és **fajok** az öt oszlopok jelölik.

    ![Oszlopok átnevezése](media\azure-stack-solution-machine-learning\image33.png)

1.  Az egyes értékek megszámlálása:

    1.  Jelölje ki a **Fajok** oszlopot.

    2.  Kattintson a jobb gombbal a kiválasztásához.

    3.  Válassza ki **értékek száma** a menüből.

        Megnyílik a **Vizsgálók** panel az adatok alatt. Megjelenik egy Négysávos hisztogramot. A céloszlop négy különféle értékkel rendelkezik: **Iris-virginica**, **Iris-versicolor**,**Iris-setosa**, és a egy **(null)** értéket.

    ![Az Értékek számának kiválasztása](media\azure-stack-solution-machine-learning\image34.png)

    ![Értékek száma hisztogram](media\azure-stack-solution-machine-learning\image35.png)

1.  A null értékek kiszűréséhez válassza a (null) sávot, majd a mínuszjelet (**-**).

    Ezután a (null) sor szürkévé válik, ami jelzi, hogy ki lett szűrve.

    ![Null értékek kiszűrése](media\azure-stack-solution-machine-learning\image36.png)

1.  Figyelje meg a **LÉPÉSEK** panelen részletezett egyes adatelőkészítési lépéseket. Átnevezi az oszlopokat, és szűr a rendszer a nullértékű sorokat, minden műveletet adat-előkészítési lépésként rögzíti. Módosíthatja a beállításaikat, átrendezheti a lépéseket, és távolítsa el a lépéseket az egyes lépések szerkesztésével.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image37.png)

1.  Zárja be az adatelőkészítés-szerkesztőt. Válassza az **x** ikont a diagramikonnal ellátott **iris-1** lapon a lap bezárásához. A munkahelyi rendszer automatikusan menti a **iris-1.dprep** fájl alatt látható a **adat-Előkészítések** fejléc.

    ![Bezárás](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>Adat-előkészítési csomagok meghívásához Python-kód létrehozása

Az adatelőkészítési csomagok kimenetét közvetlenül megvizsgálhatja a Pythonban vagy egy Jupyter notebookban. A csomagok több futtatókörnyezetben is végrehajthatók, beleértve a helyi Pythont, a Sparkot (a Dockerrel együtt) és a HDInsight-környezetet.

1.  Keresse meg az Adat-előkészítések fül alatt látható **iris-1.dprep** fájlt.

2.  Kattintson a jobb gombbal az **iris-1.dprep** fájlra, majd válassza az **Adathozzáférési kódfájl létrehozása** elemet a helyi menüből.

    ![Kód létrehozása](media\azure-stack-solution-machine-learning\image39.png)

    Egy új fájlt **iris-1.py** megnyílik az alábbi kódsorokkal az adatelőkészítési csomagként létrehozott logika meghívásához:

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    Amelyben a kód futtatási környezetétől drep a DataFrame egy eltérő jellegű jelöli:

    -  Python-futtatókörnyezetben a rendszer [pandas Dataframe](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et használ.

    -  Spark-környezetben a [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html) van használatban.

### <a name="review-irissklearnpy-and-the-configuration-files"></a>Az iris_sklearn.py és a konfigurációs fájlok áttekintése

1.  A megnyitott projektben, válassza ki a **fájlok** gombra (mappa ikon) a projektmappa fájllistájának megnyitásához a bal szélső ablaktáblán.

    ![Az Azure Machine Learning Workbench-projekt megnyitása](media\azure-stack-solution-machine-learning\image40.png)

1.  Válassza ki az **iris_sklearn.py** Python-szkriptfájlt.

    ![Szkript kiválasztása](media\azure-stack-solution-machine-learning\image41.png)

    A Workbench alkalmazásban egy új szövegszerkesztő lapon megnyílik a kód.

    > [!Note]  
    > A megjelenített kódot nem lehet pontosan ugyanaz, mint a fenti kóddal, mivel a mintaprojekt rendszeresen frissül.

    ![Fájl megnyitása](media\azure-stack-solution-machine-learning\image42.png)

1.  Vizsgálja meg közelebbről a Python-szkriptkódot, hogy megismerkedjen a kódolási stílussal.

    Az **iris_sklearn.py** szkript a következő feladatokat hajtja végre:  

    -  Betölti az alapértelmezett **iris.dprep** adatelőkészítési csomagot, és létrehoz egy [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)-et.

    -   Véletlenszerű funkciókat ad hozzá, hogy nehezítse a probléma megoldását. A véletlenszerűségre azért van szükség, mert az Iris egy kisméretű adatkészlet, amely könnyedén osztályozható közel 100%-os pontossággal.

    -  Thescikit-learnmachine tanulási kódtár használatával egy logisztikai regressziós modellt. Ez a kódtár jár az Azure Machine Learning Workbench mellé.

    -  A [pickle](https://docs.python.org/3/library/pickle.html) kódtár segítségével szerializálja a modellt egy fájlba, az `outputs` mappába.

    -  Betölti a szerializált modellt, majd deszerializálja a memóriába.

    -  A deszerializált modell segítségével előrejelzést végez egy új rekordon.

    -  Megjelenít két gráfot, egy keveredési mátrixot és egy többcsoportos ROC jellemző (ROC) görbe működő, használja a [matplotlib](https://matplotlib.org/) könyvtárba, majd menti őket theoutputsfolder. Telepítse ezt a kódtárat a környezetben, ha nem látható.

    -  Jeleníti meg automatikusan a regularizációs arány és a modell pontosságát a futtatási előzményekben. A folyamat során a `run_logger` objektumot használja a regularizációs arány rögzítésére és a pontosság modellezésére a naplókban.

### <a name="run-irissklearnpy-in-the-local-environment"></a>Az iris_sklearn.py futtatása a helyi környezetben

1.  Az Azure Machine Learning parancssori felületének (CLI) elindítása:

    1.  Indítsa el az Azure Machine Learning Workbenchet.

    2.  A Workbench menüben válassza a **Fájl**> **Parancssor megnyitása** elemet.

    Az Azure Machine Learning parancssori felülete (CLI) ablakban a projektmappában indul `C:\Temp\\myIris\` a Windows. Ez a projekt ugyanaz, mint az oktatóanyag 1. rész létrehozott.

    > [!Important]  
    > Ezzel a parancssori felülettel segítségével a következő lépéseket.

1.  A parancssori felület ablakában telepítse a ábrázolási Python-kódtár **matplotlib**, ha még nem telepítette.

    Az **iris_sklearn.py** szkript két Python-csomagban rendelkezik függőségekkel, amelyek a **scikit-learn** és a **matplotlib**. A **scikit-további** csomag telepítve van az Azure Machine Learning Workbench a kényelmi célokat szolgál. Továbbra is, a telepítés **matplotlib** lehet szükség.

    Ha a telepítése nélkül folytatja **matplotlib**, a jelen oktatóanyagban szereplő kód továbbra is sikeresen futtatható. de nem lesz képes létrehozni a keveredési mátrix kimenetét és a többcsoportos ROC-görbét az előzménymegjelenítésekben láthatóak szerint.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    A telepítése körülbelül egy percig tart.

1.  Térjen vissza a Workbench alkalmazásba.

2.  Keresse meg az **iris_sklearn.py** nevű lapot.

    ![Lap megkeresése szkripttel](media\azure-stack-solution-machine-learning\image43.png)

1.  Az eszköztáron a lap tetején válassza **helyi** a végrehajtási környezetet, futtatandó szkriptként andiris_sklearn.pyas. Előfordulhat, hogy már ki vannak választva.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image44.png)

1.  Az eszköztáron, majd enter0.01in jobb oldalán a **argumentumok** mező.

    Ez az érték megfelel a logisztikai regressziós modell regularizációs arányának.

    ![Local és szkript kiválasztása](media\azure-stack-solution-machine-learning\image45.png)

1.  Kattintson a **Futtatás** gombra. Azonnal be lesz ütemezve egy feladat. A feladat megjelenik a Workbench-ablak jobb oldalán található **Feladatok** panelen.

    ![Local és szkript kiválasztása](media\azure-stack-solution-machine-learning\image46.png)

    Néhány pillanat múlva a feladat állapotának átvált **elküldés**, az **futó**, és végül a **befejezve**.

1.  Válassza ki a **Befejezve** elemet a **Feladatok** panel állapotszöveg részén.

    ![Az sklearn futtatása](media\azure-stack-solution-machine-learning\image47.png)

    Egy előugró ablak nyílik meg, és a Futtatás standard kimenetre (stdout) szöveget jeleníti meg. A stdout szöveg bezárásához válassza a **bezárásához** (**x**) gombra a jobb felső sarkában az előugró ablakban.

    ![Standard kimenet](media\azure-stack-solution-machine-learning\image48.png)

1.  Az azonos feladat állapotánál a **feladatok** panelen válassza a kék színű **iris_sklearn.py \[n\] **(* n * Futtatás száma) feladatállapotában a  **Befejezett** állapota és a kezdési időpontot. Az ekkor megnyíló **Futtatás tulajdonságai** ablakban az adott futtatás következő adatai láthatók:

    -  A **futtatás tulajdonságaihoz** kapcsolódó információk

    -  **Kimenetek**

    -  **Metrikák**

    -  **Megjelenítések** (ha van ilyen)

    -  **Naplók**

    Amikor a futtatás befejeződik, az előugró ablakban az alábbi eredmények jelennek meg:

    > [!Note]  
    > Mivel az oktatóanyag bevezetett némi véletlenszerűsítést a gyakorlókészletbe korábban, a pontos eredmények kissé eltérhetnek az itt bemutatottaktól.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  Zárja be a **Futtatás tulajdonságai** lapot, majd térjen vissza az **iris_sklearn.py** lapra.

1.  A további futtatásoknál ismételje ezt meg.

Adjon meg `0.001` és `10` közötti értékeket az **Argumentumok** mezőben. A **Futtatás** elemet választva futtassa a kódot még néhányszor. Az argumentumérték, minden alkalommal, amikor megváltozott a logisztikai regressziós modellt a kódban, így minden alkalommal, amikor az adatkéréseket.

### <a name="review-the-run-history-in-detail"></a>A futtatási előzmények részletes áttekintése

Az Azure Machine Learning Workbenchben minden szkriptvégrehajtást futtatási előzmények rekordként. Egy adott szkript futtatási előzményeinek megtekintéséhez nyissa meg a **futtatások** megtekintése.

1.  A **Futtatások** listájának megnyitásához kattintson a **Futtatások** gombra (óra ikon) a bal oldali eszköztáron. Válassza ki **iris_sklearn.py** megjelenítéséhez a **futtatási irányítópult** ofiris_sklearn.py.

    ![Futtatásnézet](media\azure-stack-solution-machine-learning\image49.png)

1.  Megnyílik a **Futtatási irányítópult** lap.

    Áttekintheti a több futtatás során rögzített statisztikákat. Gráfok a lap tetején jelennek meg. Minden futtatásához tartozik egy egymást követő számot, és a Futtatás részletei a képernyő alján a táblázatban láthatók.

    ![Futtatási irányítópult](media\azure-stack-solution-machine-learning\image50.png)

1.  Szűrheti a táblázatot, majd az egyes diagramokra kattintva megtekintheti a futtatások állapotát, időtartamát, pontosságát és regularizációs arányát.

2.  A **Futtatások** táblázatban jelölje be két vagy több futtatás jelölőnégyzetét. Válassza az **Összehasonlítás** gombot a részletes összehasonlító ablaktábla megnyitásához. Tekintse át az összehasonlító táblázatot.

3.  Az **Összehasonlítás** ablaktábla bal felső részén található **Futtatási lista** gombra kattintva térjen vissza a **futtatási irányítópulthoz**.

    ![Vissza a Futtatási listához](media\azure-stack-solution-machine-learning\image51.png)

1.  Kijelölhet egy adott futtatást a futtatás részletes nézetének megtekintéséhez. Figyelje meg, hogy a **Futtatás tulajdonságai** szakaszban megjelennek a kiválasztott futtatás statisztikái. A kimeneti mappába írt fájlok szerepelnek a **kimenetek** szakaszt, és a fájlok letöltése onnan.

    ![Futtatás részletei](media\azure-stack-solution-machine-learning\image52.png)

A két grafikon, a keveredési mátrix és a többcsoportos ROC-görbe a **Képi megjelenítések** szakaszban látható. A naplófájlokat is megtalálhatja a **Naplók** szakaszban.

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Szkriptek futtatása az Azure Machine Learning (gépi tanulás) Workbench parancssori felület ablakában

1.  Az Azure Machine Learning parancssori felületének (CLI) elindítása:

    1.  Indítsa el az Azure Machine Learning Workbenchet.

    2.  A Workbench menüben válassza a **Fájl** > **Parancssor megnyitása** elemet.

    A CLI projektmappában indul el a projektmappában `C:\\Temp\\myIris` a Windows. Ez az a projekt létrehozása az oktatóanyag 1. rész.

    > [!Important]  
    > Ezzel a parancssori felülettel segítségével a következő lépéseket.

1.  A parancssori felület ablakában jelentkezzen be az Azure-ba. [További információk az „az login” parancsról](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    Hagyja ki ezt a lépést, ha már bejelentkezett.

1.  A parancssorba írja be a következőt:

    ```CLI
        az login
    ```

    Ez a parancs visszaad egy kódot, használja a böngészőben [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin).

1.  Lépjen a [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) a böngészőben. Írja be a kódot, amelyet a parancssori felületen kapott cégtől kapott.

    A Workbench alkalmazás és parancssori felület független hitelesítőadat-gyorsítótárakat használ, az Azure-erőforrások hitelesítésekor. Hitelesítés, nem szükséges újra amíg a gyorsítótárazott jogkivonat le nem jár.

1.  Ha a szervezete több Azure-előfizetéssel (vállalati környezetben), állítsa be a használni kívánt előfizetést. Keresse meg az előfizetést, állítsa be az előfizetés-Azonosítót, és tesztelje azt.

1.  Minden ezzel a paranccsal használt Azure-előfizetés listázása:

    ```CLI
        az account list -o table 
    ```

    A **az fióklista** parancs visszaadja a bejelentkezéséhez elérhető előfizetések listáját. Ha egynél több, azonosítsa a használt előfizetés az előfizetés azonosítóértékét.

1.  Állítsa be alapértelmezett fiókként használt Azure-előfizetés:

    ```CLI
        az account set -s <the-subscription-id
    ```

    Ahol az < a-subscription-id > használt előfizetés-azonosítóját. A zárójeleket hagyja el.

1.  Az aktuális előfizetés részleteinek lekérésével ellenőrizze az új előfizetés beállítását.

    ```CLI
        az account show
    ```

1.  A parancssori felület ablakában próbaképpen küldje el az **iris_sklearn.py** szkriptet.

    Az iris_sklearn.py végrehajtását a helyi számítási környezetben vonatkozóan fut le.

1.  Windows rendszeren:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  MacOS rendszeren:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    A kimenet hasonló lesz: 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  Tekintse át a kimenetet. A szkript futtatásakor a Workbench összes kell lennie az azonos kimeneti és eredményeket.

7.  Térjen vissza a Workbenchbe, ahol:

    Válassza ki a bal oldali ablaktáblán a mappa ikont a projektfájlok felsorolásához.  Nyissa meg a **run.py** nevű Python-szkriptet. Ez a szkript akkor hasznos, ha különböző regularizációs arányokat. 

    ![Vissza a futtatási listához](media\azure-stack-solution-machine-learning\image53.png)

1.  Futtassa többször is a kísérletet ezekkel az arányokkal.

    A szkript elindít` aniris_sklearn.pyjob` együtt a regularizációs arány o `10.0` (nevetségesen nagy szám). A parancsfájl majd felezi az arányt – a következő futtatáskor, és így tovább, félig, amíg az arány el nem kisebb, mint a nem `0.005`. A szkript a következő kódot tartalmazza:

    ![Vissza a Futtatási listához](media\azure-stack-solution-machine-learning\image54.png)

1.  Futtassa a **run.py** szkriptet a parancssorból a következőképpen:

    ```CLI
        python run.py
    ```
Ez a parancs elküldi `iris_sklearn.py` többször, különböző szabályozási arányokkal

Amikor `run.py` futtatása befejeződik, más érdekes mérőszám-diagramok a a Workbench futtatási előzmények listanézetében jelennek meg.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Az egy Ubuntu-alapú adatok adatelemzési virtuális gépet (DSVM) parancsfájlok futtatása az Azure-ban

A parancsfájl végrehajtása a Docker-tárolóban egy távoli Linux gépen SSH-hozzáférés (felhasználónév és jelszó) van szükség, hogy a távoli gép futtatásához. Az is szükséges továbbá, hogy a gépen a Docker-motor telepítve legyen és fusson.

1.  Szerkessze a létrejött<your DSVM Name>.runconfigfile underaml_configand módosítsa a keretrendszert az alapértelmezett valuePySparktoPython:

    ```yaml  
    Framework: Python
    ```
1.  Adja ki ugyanazt a parancsot a parancssori felület ablakában, mint korábban az a cél*<DSVM>* ezúttal az iris_sklearn.py végrehajtása távoli Docker-tárolóban: (helyettesítse be a <DSVM> és az adatelemző virtuális gép nevét, a szögletes zárójelek nélkül).

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

A parancs végrehajtása során, ha a adocker-pythonenvironment, azzal a különbséggel a végrehajtási távoli Linux rendszerű virtuális gépen történik. A parancssori felület ablak ugyanazt a kimeneti információt jeleníti meg.

### <a name="download-the-model-pickle-file"></a>A modell pickle-fájljának letöltése

Az oktatóanyag előző részében az **iris_sklearn.py** szkriptet helyileg, az Azure Machine Learning Workbenchben futtattuk. Ez a művelet szerializálta a logisztikai regressziós modellt a népszerű Python-alapú objektumszerializáló csomag, a [pickle](https://docs.python.org/3/library/pickle.html) használatával.

1.  Nyissa meg a Machine Learning Workbench alkalmazást. Nyissa meg a **myIris** az oktatóanyag-sorozat előző részeiben létrehozott projekt.

2.  A projekt megnyitása után válassza ki a **fájlok** gombra (mappa ikon) a bal oldali ablaktáblán a projektmappa fájllistájának megnyitásához.

3.  Válassza ki az **iris_sklearn.py** fájlt. A Workbench alkalmazásban egy új szövegszerkesztő lapon megnyílik a Python-kód.

4.  Tekintse át az **iris_sklearn.py** fájlt és keresse meg, hol jött létre a pickle-fájl. A Control+F billentyűkombinációval nyissa meg a **Keresés** párbeszédpanelt, és a Python-kódban keresse meg a **pickle** szót.

Ez a kódrészlet megmutatja, hogyan jött létre a pickle kimeneti fájlja. A kimeneti pickle-fájl neve a lemezen **model.pkl**.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  Keresse meg a modell pickle-fájlját egy korábbi futtatás kimeneti fájljai között.

    Ha a **iris_sklearn.py** szkript futtatásakor a modellfájl íródik a **kimenete** nevű mappa **model.pkl**. Ebben a mappában találhatók a végrehajtási környezetben választotta, futtassa a szkriptet, és nem a helyi projektmappában. 

    1. A fájl megkereséséhez jelölje ki azt a **futtatások** gombra (óra ikon) a bal oldali ablaktáblán nyissa meg a listáját **minden Futtatás**.  

    2. Ekkor megnyílik a **Minden futtatás** lap. A futtatások tábláján válassza ki valamelyik közelmúltbeli futtatást, ahol a cél értéke **helyi** és a szkript neve **iris_sklearn.py**.  

    3. Megnyílik a **Futtatás tulajdonságai** ablaktábla. A panel a jobb felső területen figyelje meg, hogy a **kimenetek** szakaszban. d\. A pickle-fájl letöltéséhez jelölje be a a **model.pkl** fájlt, és válassza ki **letöltése**. Mentse a fájlt a projektmappa gyökérkönyvtárába. A fájl a későbbi lépések során szükség lesz.  

    ![A pickle-fájl letöltése](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>Pontozó szkript és sémafájlok lekérése

A modellfájl mellett a webszolgáltatás üzembe helyezéséhez pontozó szkript van szükség. Ha szeretné a webszolgáltatás bemeneti adataihoz tartozó séma van szükség. A pontozó szkript betölti a **model.pkl** fájlt az aktuális mappából, és a segítségével új előrejelzéseket állít elő.

1.  Nyissa meg a Machine Learning Workbench alkalmazást. Nyissa meg a **myIris** az oktatóanyag-sorozat előző részében létrehozott projekt.

2.  A projekt megnyitása után válassza ki a **fájlok** gombra (mappa ikon) a bal oldali ablaktáblán a projektmappa fájllistájának megnyitásához.

3.  Válassza a **score_iris.py** fájlt. Megnyílik a Python-szkript. Ezt a fájlt használjuk pontozófájlként.

    ![Pontozófájl](media\azure-stack-solution-machine-learning\image56.png)

1.  A sémafájl lekéréséhez futtassa a szkriptet. Válassza ki a **helyi** környezetet és a **score_iris.py** szkriptet a parancssorban, majd válassza a **Futtatás** lehetőséget.

    Ez a szkript létrehoz egy JSON-fájlt a **kimenetek** rész, amely a modellhez szükséges sémát rögzíti.

1.  Figyelje meg a **Projekt-irányítópult** panel jobb oldalán található **Feladatok** panelt. Várjon, amíg a legújabb ** score_iris.py** feladat megjelenítése a zöld **befejezve** állapotát. Ezután kattintson a legfrissebb feladatfuttatáshoz tartozó **score_iris.py** hiperhivatkozásra a futtatási részletek megtekintéséhez.

2.  A **Futtatás tulajdonságai** lap **Kimenetek** részében válassza ki az újonnan létrehozott **service_schema.json** fájlt. Jelölje be a fájl neve melletti jelölőnégyzetet, majd válassza a **Letöltés** gombot. Mentse a fájlt a projektmappa gyökérkönyvtárába.

3.  Térjen vissza az előző lapra, és a **score_iris.py** parancsfájlt. Az adatgyűjtés, modellbemenetek és előrejelzések a webszolgáltatásból rögzíthetők. A következő lépések érdekesek az adatgyűjtés.

4.  Tekintse át a kódot, amely a fájlimportálási osztály tetején **ModelDataCollector**. A Modelladat-gyűjtési funkciót tartalmazza:

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  Tekintse át a következő, a **ModelDataCollector** osztályt példányosító kódsorokat az **init()** függvényben:

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  Tekintse át a következő, bemeneti és előrejelzési adatokat gyűjtő kódsorokat a **run(input_df)** függvényben.

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

Készítse elő a környezetet a modell üzembe helyezése.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>5. lépés: Központi telepítése, és az Azure Container Registry használata

Telepítéséhez és használatához az Azure Container Registrybe.

Hozzon létre egy Azure tárolóregisztrációs adatbázist az **az acr create** paranccsal. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az erőforráscsoport megegyezik.

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Az **az acr login** paranccsal jelentkezzen be az ACR-példányba. Adja meg a tárolóregisztrációs adatbázis egyedi nevét, amelyet a létrehozásakor adott meg.

    ```CLI
        az acr login --name <acrName>
    ```

A parancs visszaadja a "bejelentkezés sikeres üzenetet, ha befejeződött.

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>Üzembe helyezés helyi fejlesztési és tesztelési a szolgáltatás előkészítése

Használat *helyi módú* üzembe helyezés a helyi számítógépen, és a fejlesztési és tesztelési a Docker-tárolók futtatásához.

A Docker Engine-nek helyileg kell futnia a modell üzembe helyezése következő lépéseinek végrehajtásához. Használja a `-h` jelző végén található minden egyes parancsot a megfelelő súgóüzenetet megjelenítéséhez.

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  Nyissa meg a parancssori felületet (CLI) A Machine Learning Workbench alkalmazás **Fájl** menüjében válassza a **Parancssor megnyitása** lehetőséget.

    A parancssor az aktuális projektmappában nyílik **c:\\temp\\myIris**.

1.  Győződjön meg arról, hogy az Azure erőforrás-szolgáltató **Microsoft.ContainerRegistry** regisztrálva van az előfizetésben. Az erőforrás-szolgáltató regisztrálása létrehozása egy környezetet a 3. lépés előtt. Ellenőrizze, hogy ha már regisztrálva van a következő paranccsal:

    ```CLI
        az provider list --query "\[\].{Provider:namespace, Status:registrationState}" --out table
    ```

    Ez a kimenet megtekintéséhez:

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    Ha **Microsoft.ContainerRegistry** nem nem regisztrált használja a következő parancsot:

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    A regisztráció eltarthat pár percig. Ellenőrizze a regisztrációs állapot az előző **az identitásszolgáltató-listája** vagy a következő parancsot:

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    A kimenet harmadik sora a következő: **"registrationState": "Registering"**. Várjon néhány pillanatot, és ismételje meg a **megjelenítése** parancsot, amíg a kimenet **"registrationState": "Registered.**

1.  Hozza létre a környezetet. Ezt a lépést környezetenként egyszer futtatni.

    A következő telepítési parancs szükséges közreműködői hozzáférés szükséges az előfizetéshez. Közreműködői hozzáférés az erőforráscsoporthoz való üzembe helyezés megadása kötelező. Adja meg az erőforráscsoport nevét a setup parancs részeként a gflag használatával.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    Kövesse a képernyőn megjelenő utasításokat, amelyekkel üzembe helyezhet egy tárfiókot Docker-rendszerképek tárolásához, egy Azure Container Registryt Docker-rendszerképek listázásához és egy Azure Application Insights-fiókot telemetria gyűjtéséhez. **Ha használja a cswitch, a parancs létrehoz ezen felül egy Tárolószolgáltatási fürtöt**.

    A fürt nevét nem azonosítható a környezet. A hely legyen ugyanaz, mint a az Azure Portalról létrehozott Modellkezelési fiók helye.

    Ha ellenőrizni szeretné, hogy a környezet telepítése sikeres volt-e, a következő paranccsal ellenőrizheti az állapotot:

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    Győződjön meg arról, hogy "Provisioning State" értéke "Succeeded", ahogy látható, mielőtt beállítaná a környezetet az 5. lépés:

    ![Kiépítési állapot](media\azure-stack-solution-machine-learning\image57.png)

1.  Állítsa be a környezetet.

    A beállítás befejezése után az alábbi paranccsal állíthatja be a környezet üzembe helyezéséhez szükséges környezeti változókat. Használja ugyanazt a környezetnevet korábban a 3. lépésben használt. Használja ugyanazt az erőforráscsoport-nevet, amelyet a beállítási folyamat végeztével a parancsablakban kimenetként kapott.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  Az üzembe helyezett környezetet a helyi webszolgáltatás üzembe megfelelő konfigurálását írja be a következő parancsot:

    ```CLI
    az ml env show
    ```

    Hozza létre a valós idejű webszolgáltatás.

    > [!Note]  
    > Újból felhasználhatja a Modellkezelési fiókot és környezetet a webszolgáltatások későbbi üzembe helyezéséhez. Hiba esetén nem kell őket minden webszolgáltatáshoz létrehozásához. Egy fiókkal vagy környezettel több webszolgáltatás is lehet társítva.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Valós idejű webszolgáltatás létrehozása külön parancsokkal

Alternatív megoldásként a **az ml service létre valós idejű** korábban látható lépéseket is végezni külön-külön parancsot.

Először regisztrálja a modellt. Ezután hozza létre a jegyzéket, állítsa össze a Docker-rendszerképet, és hozza létre a webszolgáltatást. A lépésenkénti megközelítéssel nagyobb rugalmasságot biztosít minden lépésnél. Ezenkívül felhasználhatók az előző lépésekben létrehozott entitásokat.

1. Regisztrálja a modellt a pickle-fájl nevének megadásával.

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    Ez a parancs létrehoz egy modellazonosítót.

2.  Hozzon létre egy jegyzéket.

    Jegyzék létrehozásához használja a következő parancsot, és adja meg az előző lépésből származó modellazonosító-kimenetet: 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    Ez a parancs létrehoz egy jegyzékazonosítót.

3.  Hozzon létre egy Docker-rendszerképet.

    Docker-rendszerkép létrehozásához használja a következő parancsot, és adja meg a jegyzékfájl kimenetét az előző lépésben. A conda-függőségeket is használható keresztül a `-c` váltani. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    Ez a parancs létrehoz egy Docker-rendszerképazonosítót.

2.  Hozza létre a szolgáltatást.

    Létrehoz egy szolgáltatást, használja a következő parancsot, és adja meg a lemezkép-azonosító kimenetét az előző lépésből: 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    Ez a parancs létrehoz egy webszolgáltatás-azonosítót.
    
    Ezután futtassa a webszolgáltatást.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>6. lépés: Kubernetes-fürt üzembe helyezése az Azure Stackhez

Kubernetes-fürt üzembe helyezése az Azure Stackhez.

Kubernetes az Azure Container szolgáltatások (ACS) Azure Stack-motor által létrehozott Azure Resource Manager-sablonok használatával lehet telepíteni. [*Kubernetes* ](https://kubernetes.io/) üzembe helyezés automatizálásához egy nyílt forráskódú rendszer méretezés, és a tárolókban található alkalmazások felügyeletét. A [ *tároló* ](https://www.docker.com/what-container) az a képen hasonló virtuális gép szerepel. Egy virtuális Gépet, ellentétben a tároló rendszerképét tartalmazza az erőforrásokat, az alkalmazás, például a kódot, a kód, az adott könyvtárakat és a beállítások végrehajtásához futásidejű futtatásához.

A Kubernetes segítségével:

 -  Rugalmasan méretezhető, bővíthető, másodpercek alatt telepíthető alkalmazásokat fejleszthet.

 -  Leegyszerűsítheti az alkalmazást, és a megbízhatóság javításához különböző Helm-alkalmazások. [*Helm* ](https://github.com/kubernetes/helm) egy nyílt forráskódú csomagolás eszköz, amellyel a telepítése és a Kubernetes-alkalmazások életciklusának kezeléséhez.

 -  Könnyedén figyelheti és diagnosztizálhatja a révén az alkalmazások állapotát, és frissítés funkcióit.

> [!Note]  
> A fürtszolgáltatás telepítése a lépnek körülbelül egy óra alatt adjon csomag megfelelően.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Kubernetes-fürt üzembe helyezése előfeltételei

Első lépésként engedélyek és az Azure Stack készültségi megerősítése:

1.  Ellenőrizze, hogy a **(előzetes verzió) Kubernetes-fürt létrehozása** elem érhető el az Azure Stack piactéren. Ha ez az elem nem található, és ez az elem hozzáadása az Azure Stack-környezet egy Azure Stack-operátorokról működnek.

2.  Alkalmazások létrehozása az Azure Active Directory (Azure AD) bérlő ellenőrzése. Engedélyek szükségesek a Kubernetes-telepítéshez.

    Az engedélyek ellenőrzése kapcsolatos utasításokért lásd: [ *ellenőrizze az Azure Active Directory-engedélyek*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

3.  Hozzon létre nyilvános és titkos ssh-kulcs, jelentkezzen be a Linux rendszerű virtuális gép az Azure Stacken. A nyilvános kulcsot a fürt létrehozásakor van szükség. További utasítás: [hitelesítési kulcs létrehozásához az SSH számára](#generate-an-authenticatio-key-for-ssh).

4.  Ellenőrizze, hogy az előfizetés az Azure Stack-bérlői portálon érvényes, és nincsenek elegendő nyilvános IP-címek adhatók hozzá az új alkalmazások.

    A fürt nem telepíthető az Azure Stackkel **rendszergazda** előfizetés. Használja a **felhasználói** előfizetés.

###  <a name="generate-an-authentication-key-for-ssh"></a>Hitelesítési kulcs létrehozásához az SSH-hoz

A munkamenet a Linux Windows alrendszere belül használja a következő parancsokat a hitelesítési kulcs létrehozásához: 

1. Típus:

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. Válassza ki `id_rsa` amikor a rendszer kéri. 
3. Hozzon létre egy jelszót, amikor a rendszer kéri. Fontos megjegyzés: ezt a jelszót a későbbi használatra. 
    A kimenet alábbi hasonlóan néz ki: 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image58.png)

4. A kulcs létrehozása után illessze be a fontos információ a következő parancsokkal: 
    ```Bash
    cat id_rsa.pub
    ```
    A kimenet alábbi hasonlóan néz ki: 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. Másolja ki a létrehozott kulcs az SSH nyilvános kulcs mezőbe.

### <a name="create-a-service-principal-in-azure-ad"></a>Egyszerű szolgáltatás létrehozása az Azure ad-ben

1.  Jelentkezzen be globális [ *az Azure portal*](http://www.poartal.azure.com/).

2.  Jelentkezzen be az Azure Stack-példányhoz társított Azure AD-bérlővel.

3.  Hozzon létre egy Azure AD-alkalmazást.

    1. Válassza ki **az Azure Active Directory** > **+ Alkalmazásregisztrációk**> **új Alkalmazásregisztráció**.
    2. Adjon meg egy **neve** az alkalmazás. 
    3. Válassza ki **webalkalmazás / API**. 
    4. Adja meg `http://localhost` számára a **bejelentkezési URL-**. 
    5. Kattintson a **Létrehozás** gombra.

1.  Jegyezze fel az **alkalmazás azonosítóját**. Ez az azonosító akkor van szükség, a fürt létrehozásakor és hivatkozott **egyszerű szolgáltatás ügyfél-azonosító**.

2.  Válassza ki **beállítások** > **kulcsok**.

    1. Adja meg a **leírás**. 
    2. Válassza ki **soha nem jár le** a **lejárat**. 
    3. Kattintson a **Mentés** gombra. Győződjön meg, vegye figyelembe a kulcs karakterláncát. A kulcs karakterláncát van szükség, a fürt létrehozásakor hivatkozik és a **egyszerű szolgáltatás titkos Ügyfélkód**.

### <a name="give-the-service-principal-access"></a>A szolgáltatás egyszerű hozzáférést

A szolgáltatás egyszerű hozzáférést biztosít az előfizetés, előfordulhat, hogy létre erőforrásokat.

1.  Jelentkezzen be a [felügyeleti portálján](https://adminportal.local.azurestack.external/).

2.  Válassza ki **további szolgáltatások** > ** felhasználói előfizetések ** > **+ Hozzáadás**.

3.  Válassza ki a létrehozott előfizetés.

4.  Válassza ki **hozzáférés-vezérlés (IAM)** > Válasszon **+ Hozzáadás**.

5.  Válassza ki a **tulajdonosa** szerepkör.

6.  Válassza ki az egyszerű szolgáltatásnév a szolgáltatás számára létrehozott alkalmazás nevét. Szükség esetén adja meg a nevét a keresőmezőbe.

7.  Kattintson a **Mentés** gombra.

8.  Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).

9.  Válassza ki **+ új** > **számítási** > **Kubernetes-fürt**. Kattintson a **Létrehozás** gombra.

    ![Megoldássablon telepítése](media\azure-stack-solution-machine-learning\image59.png)

10\. Válassza ki **alapjai** a a Kubernetes-fürt létrehozása.

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. Adja meg a **Linux rendszerű virtuális gép rendszergazdai felhasználónevét**. A Linux rendszerű virtuális gépek, a Kubernetes-fürt részét képező és a DVM felhasználóneve.

12. Adja meg a **SSH Public Key** használt a hitelesítéshez a Kubernetes-fürt és a DVM részeként létrehozott összes Linux rendszerű gépen.

13. Adja meg a **fő profil DNS-előtagja** , amely egyedi a régióban. Ez a terület-egyedinek kell lennie nevével, például `ask8s-12345`. Válassza ki, próbálja meg ugyanaz, mint az erőforráscsoport neve ajánlott eljárás.

    > [!Note]  
    > Ha mindegyik fürthöz egy új és egyedi fő profil DNS-előtagot használja.

14. Adja meg a **készlet profil ügynökeinek**. A száma a fürtben található ügynökök számát tartalmazza. Lehet 1-4.

15. Adja meg a **szolgáltatásnév ClientId** ezt használja a Kubernetes Azure felhőszolgáltató.

16. Adja meg a **egyszerű szolgáltatás titkos Ügyfélkód** jön létre, amikor a szolgáltatás egyszerű alkalmazás létrehozásához.

17. Adja meg a **Kubernetes az Azure Cloud szolgáltató verziója**. Ez az a verzió a Kubernetes Azure-szolgáltatóhoz. Az Azure Stack kiad egy egyéni Kubernetes-build minden egyes Azure Stack-verzió.

18. Válassza ki a **előfizetés** azonosítóját.

19. Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket.

20. Válassza ki a **hely** az erőforráscsoport. Ez az a régió, az Azure Stack-telepítés kiválasztott.

### <a name="specify-the-azure-stack-settings"></a>Az Azure Stack-beállításainak megadása

1.  Válassza ki a **blokk beállításait az Azure Stack**.

    ![Megoldássablon telepítése](media\azure-stack-solution-machine-learning\image61.png)

2.  Adja meg a **bérlői Azure Resource Manager-végpont**. Ez az az Azure Resource Manager-végpont hozza létre az erőforráscsoportot, a Kubernetes-fürthöz való kapcsolódáshoz. A végpont az az Azure Stack-operátorokról integrált rendszer szükséges. Az az Azure Stack Development Kit (ASDK), használjon `https://management.local.azurestack.external`.

3.  Adja meg a **Bérlőazonosító** a bérlő számára. Lásd: [ *Bérlőazonosító beszerzése* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) értéket meg kell keresni a bérlő azonosítója.

### <a name="install-kubectl-on-windows-powershell-environment"></a>A kubectl telepítése a Windows PowerShell-környezet

A WSL környezetben futtassa a következő parancsokat a kubectl telepítése a WSL-környezetben.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>A Windows alrendszer a kubectl telepítése Linux-környezet

A WSL-környezetben, futtassa a következő parancsokat a WSL környezetben kubectl telepítéséhez.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>A kubectl konfigurálása

Ahhoz, hogy megtalálhatja és elérheti a Kubernetes-fürthöz a kubectl-*kubeconfig fájl* van szükség. A rendszer automatikusan létrehozza a fürt létrehozásakor kube-up.sh használatával, vagy Minikube-fürt üzembe helyezése. Tekintse meg a [ *első lépések útmutató* ](https://kubernetes.io/docs/setup/) további információt a fürtök létrehozása során. Hozzáférés a fürt egy másik felhasználó által létrehozott, tekintse meg a [ *megosztás fürthöz hozzáférést dokumentum*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). Alapértelmezés szerint a kubectl konfigurálása a következő helyen található **~.kube/config**.

### <a name="check-the-kubectl-configuration"></a>Ellenőrizze a kubectl konfigurálása

Ellenőrizze, hogy a kubectl, ezt pedig a fürt állapota megfelelően van konfigurálva:

```Bash  
kubectl cluster-info
```

a kubectl megfelelően van konfigurálva, aki a fürtöt, ha az URL-cím-válasz jelenik meg.

a kubectl nincs megfelelően konfigurált vagy nem tud csatlakozni egy Kubernetes-fürtöt, ha a következő üzenet jelenik meg:

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

Például egy Kubernetes-fürtön futó helyi laptopon, amikor egy eszköz is szükség lehet (minikube vagy hasonló) újra a fenti parancsok futtatásához.

Ha a kubectl fürt-adatok az URL-cím választ ad vissza, de a fürt a rendszer továbbra is nem érhető el, ellenőrzi a megfelelő konfiguráció használatával:

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>Shell autocompletion engedélyezése

a kubectl autocompletion támogatja, így shell lehetővé tétele, gyors és egyszerű is.

Magát a befejezési parancsfájlt kubectl által létrehozott és érhető el a profilt.

### <a name="connect-to-the-kubernetes-cluster"></a>A Kubernetes-fürthöz való csatlakozáshoz

Csatlakozás a fürthöz, a Kubernetes parancssori ügyfelét (**kubectl**) van szükség. Ehhez útmutatást csatlakozik, és a fürt kezeléséhez [Azure Container Services dokumentációja.](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

Csatlakozás a Kubernetes-fürt minden SSH-ügyfél használható. Windows-alrendszer Linux (WSL) használata ajánlott. A gép, a Kubernetes-fürthöz csatlakozik a fürthöz létrehozott erőforráscsoportot lesz, és az előtag vmd – edge-ml-verem kezdődik.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Miután csatlakozott a Kubernetes-gép, futtassa a következő gép Kubernetes konfigurációs fájl beolvasása.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

A kimenet az alábbihoz hasonlóan fog jelenik:

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Másolja a fájlok elérési adatait, és futtassa a következő parancsot, és illessze be a fenti másolt kubeconfig elérési:

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

A kimenet egy nagy blokkblobok, a szöveg, amely a nyers JSON-tartalmak lesz. Másolja a szöveget, és illessze be ezt a kódot egy Visual Studio fájlba mentése JSON-fájlként. Az eredmény egy helyileg tárolt kubeconfig.json fájlt. (a/mnt/c/felhasználó/save <current user> /dokumentumok vagy a könyvtár Kube mint kubeconfig.json)

### <a name="configure-the-kubernetes-cluster"></a>A Kubernetes-fürt konfigurálása

Egy új WSL-munkamenetben a helyi JSON-fájl beszerzését követően használja a következő parancsokat a fürt konfigurálása.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

Kubernetes konfigurációs beállítások meghatározva (lásd az alábbi kimenetben).

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image62.png)

A helyi proxy szolgáltatás elindítása:

```Bash  
kubectl proxy
```

Tallózással keresse meg a kubernetes fürt felhasználói Felületét a következő címen: `https://localhost:8001`.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image63.png)

Most már üzembe helyezéséhez a tároló és a egy tároló, amely abban a felhőben, amelyek a helyszínen látható, ahol egy helyen.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image64.png)

Testre szabhatja a **iris_deployment.yaml** fájlt (található /*mnt/c/felhasználók/<current user>/dokumentumok/directory Kube*) így **webservicename** és tárolók  **Kép** és **neve** felel meg a központi telepítéshez választott bármilyen kód szerkesztővel.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image65.png)

A tároló portját állítsa **5001.**

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image66.png)

Majd hozza létre a **imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>A fürt, amely tartalmazza az engedélyezési jogkivonat titkos kulcs létrehozása

Egy Kubernetes-fürtöt használ, a titkos kulcs **docker-registry** írja be a hitelesítést egy tároló-beállításjegyzék egy privát rendszerkép lekérése.

Hozzon létre a titkos kód elnevezése **azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

Keresés:

- **< a-registry-server >** van az Azure Container Registry **bejelentkezési kiszolgáló**.
- **< saját-neve >** van az Azure Container Registry **felhasználónév**.
- **< a pword >** van az Azure Container Registry **jelszó**. Győződjön meg a jelszót az idézőjelek között.
- **< az e-mail >** a tároló írási/olvasási hozzáféréssel rendelkező felhasználó.
- Ezt az információt találja meg a **Azure Container** **beállításjegyzék** alatt **Tárelérési kulcsok**.
- Docker hitelesítő adatok mostantól állítsa be a fürtben, a titkos kulcs nevű **azuremlcr**.

Mentse a **iris_deployment.yaml** fájl (található /*mnt/c/felhasználó/<current user>/dokumentumok/directory Kube*).

### <a name="create-the-kubernetes-container"></a>A Kubernetes-tároló létrehozása

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

Üzembe helyezés állapotának ellenőrzéséhez:

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

Az üzembe helyezés hosszabb ideig is eltarthat.

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>Visual Studio Team Services az automatikus központi telepítés konfigurálása

#### <a name="create-a-team-project"></a>Létrehozhat egy csoportprojektet

1.  Győződjön meg, hogy [projekt gyűjtemény a Rendszergazdák csoport tagságát.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) Csapatprojektek, létrehozásához **hozható létre új** engedély értékre kell állítani **engedélyezése**.

2.  Válassza ki a projektek oldaláról **új projekt**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image69.png)

1.  Adja a projektnek **HybridMLIris**.

2.  Válassza ki a kezdeti verziókövetési típusa szerint **Git**

3.  Válasszon ki egy folyamat, és válassza ki **létrehozás**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>Importálja egy kódrészletet tárház létrehozása

Szükség van egy Git-tárház YAML-kódot.

#### <a name="add-user-to-the-git-repo"></a>Felhasználó hozzáadása a GIT-adattár

1.  Jelölje ki az alapértelmezett projekt-irányítópult készítése a Git hitelesítő adatokat.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image71.png)

1.  Adja meg a jelszót, ha szükséges, és mentse el a Git hitelesítő adatokat.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image72.png)

1.  A tárház inicializálása kiválasztásával a **inicializálása** gomb és létrehozása egy **információs** fájlt.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>A Git-tárház helyi klón, és töltse fel a kódot. 

1.  Egy könyvtárat a gépen, győződjön meg arról, `c:\\users\\<User>\\source\\repos\\hybridMLIris`, és a tárház klónozása

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image74.png)

1.  Navigáljon az újonnan klónozott tárház:

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image75.png)

1.  Másolás a **iris_deployment.yaml** fájlt a tárházba.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  Véglegesítse a módosítást a GITBEN

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>A saját Build és kiadás ügynök előkészítése a VSTS-integrációval

#### <a name="prerequisites"></a>Előfeltételek

VSTS hitelesíti, szemben az Azure Resource Manager használatával egy egyszerű szolgáltatást. A VSTS tudják üzembe erőforrásokat az Azure Stack-előfizetéshez szükséges közreműködői állapota. \ **az alábbiakban a magas szintű lépéseket, amelyek az ilyen-hitelesítés engedélyezéséhez konfigurálni kell:**

1.  Egyszerű szolgáltatást kell létrehozni, vagy egy meglévő használható.

2.  Hitelesítési kulcsok hozhatók létre a szolgáltatásnevet kell.

3.  Az Azure Stack-előfizetés keresztül szerepköralapú hozzáférés-vezérlést, hogy az egyszerű Szolgáltatásnevet, a közreműködő szerepkör részét kell ellenőrizni kell.

4.  A vsts-ben definiált új szolgáltatás az Azure Stack-végpontokra, valamint a SPN-információk segítségével kell létrehozni.

#### <a name="service-principal-creation"></a>Egyszerű szolgáltatás létrehozása

Tekintse meg a [egyszerű szolgáltatás létrehozása utasításokat](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) hozzon létre egy egyszerű szolgáltatást, és válassza a Web App és az API számára az alkalmazás típusaként.

**Hozzáférési kulcs létrehozása**

Egyszerű szolgáltatás hitelesítési kulcs használatát igényli, kövesse a lépéseket ebben a szakaszban egy kulcs létrehozásához.

1.  A **alkalmazásregisztrációk** az Azure Active Directoryban, válassza ki az alkalmazást.

    ![alkalmazás kiválasztása](media\azure-stack-solution-machine-learning\image77.png)

1.  Jegyezze fel az értékét **azonosítóját. Ha a szolgáltatásvégpont beállítása a vsts-ben az értéket használja.**

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image78.png)

1.  A hitelesítési kulcs létrehozásához válassza a **Beállítások** elemet.

    ![beállítások kiválasztása](media\azure-stack-solution-machine-learning\image79.png)

1.  Válassza a **Kulcsok** elemet.

    ![kulcsok kiválasztása](media\azure-stack-solution-machine-learning\image80.png)

1.  Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.

    ![kulcs mentése](media\azure-stack-solution-machine-learning\image81.png)

A kulcs mentése után megjelenik a kulcs értéke. Másolja ezt az értéket, később igény szerint. A **kulcs értékét** ID azonosító szükséges, jelentkezzen be az alkalmazás az alkalmazással együtt. A kulcs értékét, ahol az alkalmazás le tudja kérni Store.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>Bérlőazonosító beszerzése

A Szolgáltatásvégpontok konfigurálásának részeként a vsts-ben van szükség a **Bérlőazonosító** , amely megfelel az AAD-címtárában, az Azure Stack-blokk üzembe lett helyezve. Kövesse a lépéseket ebben a szakaszban kell gyűjtenie a bérlő azonosítója.

1.  Válassza az **Azure Active Directory** elemet.

    ![Azure Active Directory kiválasztása](media\azure-stack-solution-machine-learning\image83.png)

1.  A Bérlőazonosító lekéréséhez válassza **tulajdonságok** az Azure AD-bérlő számára.

    ![Azure AD tulajdonságok kiválasztása](media\azure-stack-solution-machine-learning\image84.png)

1.  Másolja ki a **Címtár-azonosítót**. Ez az érték a bérlő azonosítója.

    ![bérlőazonosító](media\azure-stack-solution-machine-learning\image85.png)

A szolgáltatásnév engedélyeket helyezhet üzembe erőforrásokat az Azure Stack-előfizetés

Az előfizetésben található erőforrások eléréséhez, rendeljen hozzá egy szerepkörhöz az alkalmazást. Döntse el, melyik szerepkör jelöli az alkalmazást a megfelelő engedélyekkel. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Állítsa be a hatókört az előfizetés, erőforráscsoport vagy erőforrás szintjén. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása lehetővé teszi, hogy olvassa el az erőforráscsoportot és a benne található erőforrásokat.

1.  Keresse meg az alkalmazás hozzárendelése hatókör kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **előfizetések**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  Válassza ki a **előfizetés** (erőforráscsoportra vagy erőforrásra) az alkalmazás hozzárendelése.

    ![Válasszon hozzárendelés előfizetést](media\azure-stack-solution-machine-learning\image87.png)

1.  Válassza ki **hozzáférés-vezérlés (IAM)**.

    ![Jelölje be a hozzáférés](media\azure-stack-solution-machine-learning\image88.png)

1.  Válassza a **Hozzáadás** lehetőséget.

    ![Válassza a hozzáadása](media\azure-stack-solution-machine-learning\image89.png)

1.  Válassza ki az alkalmazást hozzárendelni a szerepkört. Az alábbi képen látható a **tulajdonosa** szerepkör.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image90.png)

1.  Alapértelmezés szerint az Azure Active Directory-alkalmazások nem megjelenik az elérhető lehetőségek közül. Az alkalmazás található **nevet kell adnia** keresési mezőbe, és válassza ki azt.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image91.png)

1.  Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Az alkalmazás megjelenik az adott hatókörnél egy szerepkörhöz rendelt felhasználók listáját.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy hozzáféréskezelést az Azure és az Azure Stackben. RBAC használata esetén csak olyan mértékű hozzáférést a felhasználók a munkája elvégzéséhez is megadható. Szerepköralapú hozzáférés-vezérléssel kapcsolatos további információk: [Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

**VSTS-Ügynökkészletek**

Ahelyett, hogy minden ügynök külön-külön, ügynökök be vannak szervezve **ügynökkészletek**. Az ügynökkészlet a megosztási határt a készlet az összes ügynöknek határozza meg. Ügynökkészletek hatóköre a vsts-ben, a VSTS-fiók, ezért ezek csapatprojektek megoszthatók. További információk és létrehozni a VSTS ismertető ügynökkészletek: [Ügynökkészletek létrehozása és üzenetsorokat.](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**Az Azure Stack aPersonal hozzáférési jogkivonat (PAT) hozzáadása**

 -  Jelentkezzen be a VSTS-fiók, és válassza ki a **fiók profilhoz** nevét.

 -  Válassza ki **biztonság kezeléséhez** hozzáférési token létrehozása oldalra.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image92.png)

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image93.jpeg)

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> Szerezze be a jogkivonat adatai. Nem jelennek a képernyő elhagyása után újra.

1.  Másolás a **token**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Telepítse a VSTS ügynök létrehozása az Azure Stack-kiszolgálón üzemeltetett kiszolgáló létrehozása

1.  Csatlakozás a **hozhat létre kiszolgáló** üzembe helyezve az Azure Stack gazdagépen.

    > [!Note]  
    > Ellenőrizze, hogy az Azure Stack hozhat létre kiszolgáló üzemeltetett a nyilvános internetről érhető el. Ha nem, az Azure Stack-operátorokról hozzáférés érdekében együttműködik.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Az Ubuntu hozhat létre kiszolgáló frissítése a legújabb verzióra (18.04):

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > Ez a művelet eltarthat egy ideig.

2.  A build-kiszolgáló előfeltétel-alkalmazások telepítése. A a bash rendszerhéj az Ubuntu, a következő parancsokat alapján hozhat létre kiszolgálót futtató:

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  Töltse le és telepítse a fordító-ügynökhöz, mint a szolgáltatás használata egy **személyes hozzáférési jogkivonat (PAT)** , és futtassa a VM-rendszergazda fiókkal.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  Nyissa meg a kibontott build-ügynök mappájába. Futtassa a következő kódot.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image97.png)

2.  Miután **./config.sh**befejezése után futtassa az alábbi engedélyezéséhez a szolgáltatást a kiszolgáló rendszerindító, és indítsa el a szolgáltatást:

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

Az ügynök már látható a VSTS-mappában.

#### <a name="endpoint-creation-permissions"></a>Végpont létrehozása engedélyek

Felhasználók végpontokat hozhat létre, így VSTO buildek Azure Service alkalmazásokat telepíthet a verem. Vsts-ben a fordító-ügynökhöz, amely csatlakoztatja az Azure Stack használatával csatlakozik.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image98.png)

1.  Az a **beállítások** menüjében válassza **biztonsági**.

2.  Az a **VSTS csoportok** listája a bal oldali válassza **végpont alkotói**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image99.png)

3.  Az a **tagok lapon** válassza ki a **+ Hozzáadás**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image100.png)

1.  Típus **felhasználónév** és a felhasználónév kiválasztása a listából.

2.  Válassza ki **módosítások mentése**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image101.png)

3.  Az a **VSTS csoportok** listája a bal oldali válassza **végpont rendszergazdák**.

4.  Az a **tagok lapon** válassza ki a **+ Hozzáadás**.

5.  Adjon meg egy **felhasználónév** , és válassza ki, hogy a felhasználó a listából.

6.  Válassza ki **módosítások mentéséhez.**

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    A fordító-ügynökhöz az Azure Stackben átveszi az utasításokat a vsts Szolgáltatásból, amely majd ruház kommunikál az Azure Stack-végpontjának információit.

    Az Azure Stack kapcsolat VSTS most már készen áll.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>A Build és kiadás-definíciók konfigurálása

Most, hogy a kapcsolatot létesít, fogja manuálisan képezze le a létrehozott Azure-végpont, AKS és Azure Container Registry a buildre és kiadási definícióiról.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>A YAML-kódot a Build definíció létrehozása

1.  Válassza ki a buildek szakasz alatt a Build és kiadás eseményközpont, és hozzon létre egy új definíciót.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image104.png)

1.  A VSTS Git kiválasztása, és válassza ki a korábban létrehozott tárházba.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image105.png)

1.  Válassza ki az üres folyamatot sablonként

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image106.png)

1.  Nevezze el a Build **másolási összetevő** , és válassza ki az Azure Stack-hozhat létre kiszolgálót az ügynök várólista.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image107.png)

1.  1. fázis válassza a folyamatok, és nevezze át, hogy **másolási összetevő**, majd **feladat hozzáadása** fázisa:

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image108.png)

1.  Válassza ki **összeállítása összetevők közzététele** a a **segédprogram** listájára és válassza ki **hozzáadása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image109.png)

1.  Válassza ki a **közzététele elérési út** , és válassza ki a **iris_deployment.yaml** fájlt.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image110.png)

1.  A lehívandó összetevő neve **iris_deployment** , és válassza a közzététel hely **Visual Studio Team Services/TFS**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image111.png)

1.  Válassza ki **várólistára & mentése**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image112.png)

1.  Build állapotának ellenőrzéséhez válassza a build-azonosítót.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image113.png)

Sikeres ehhez hasonlóan fog kinézni:

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>A YAML-kódot a kiadási definíció létrehozása

1.  Válassza ki a kiadások szakasz alatt a Build és kiadás hubot, új-definíció

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image115.png)

1.  Válassza ki az üres folyamatot sablonként.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image106.png)

1.  Neve a környezet az Azure Stack.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image116.png)

1.  Adjon hozzá egy új összetevő kiválasztásával **összetevők** és **+ Hozzáadás**

2.  A forrás típusaként válassza a Build és a **HybridMLIris** a projektként.

3.  Ezután válassza ki a korábban létrehozott a forrás.

4.  Válassza ki **hozzáadása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image117.png)

1.  Válassza ki az Azure Stack környezetből, majd egy új feladat hozzáadása az Azure Stackhez

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image118.png)


1.  Az ügynök fázis állítsa be az ügynök üzenetsor, az Azure Stack üzemeltetett hozhat létre kiszolgálót.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image119.png)

1.  Ebben a fázisban egy új feladatot ad hozzá, válassza ki az üzembe helyezés az üzembe helyezés a Kubernetes feladat, és válassza a Hozzáadás.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image120.png)


1.  Nevezze el **a Kubectl a alkalmazni** (név), és válassza ki a alkalmaz parancsot.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image121.png)

    Most hozzon létre egy új Kubernetes szolgáltatás kapcsolatot.

#### <a name="create-kubernetes-service-endpoint"></a>Kubernetes Service-végpont létrehozása

1.  A Kubernetes szolgáltatáskapcsolódási, válassza a **+ új** gombra, és válassza**Kubernetes**a listából. Ez a végpont használatával csatlakozzon a**VSTS**és a**Azure Container Service (AKS)**.

2.  **Kapcsolat neve**: Adja meg a kapcsolat nevét.

3.  **Kiszolgáló URL-címe**: Adja meg a container service címet a formathttp: / / {API kiszolgálócíme}

4.  **Kubeconfig**: a Kubeconfig érték lekéréséhez futtassa a következő Azure-parancsok egy parancssorban a rendszergazdai jogosultsággal indított.

    > [!Important]  
    > Ezzel a parancssori felülettel segítségével a következő lépéseket.

6.  A parancssori felület ablakában jelentkezzen be az Azure-ba. [További információk az „az login” parancsról](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

7.  A parancssorba írja be a következőt:

    ```CLI
        az login
    ```

10. Ez a parancs visszaad egy kódot, használja a böngészőben <https://aka.ms/devicelogin>.

11. Lépjen a <https://aka.ms/devicelogin> a böngészőben. Amikor a rendszer kéri, írja be a kódot, a parancssori felületen, a böngészőben kapott.

    ![Kubernetes Service-végpont](media\azure-stack-solution-machine-learning\image122.png)

1.  Írja be a következő parancsot a parancssorban a hozzáférési hitelesítő adatok lekérése a Kubernetes-fürt.

### <a name="azure-ml-workbench-cli"></a>Az Azure Machine Learning Workbench parancssori felület

az aks get-credentials resource-group <yourResourceGroup> neve <yourazurecontainerservice>

![Kubernetes Service-végpont](media\azure-stack-solution-machine-learning\image123.png)

1.  Keresse meg a **.kube**a kezdőkönyvtár mappát (például: C:\\felhasználók\\<user>\\dokumentumok\\Kube)

2.  Másolja ki a tartalmát a**config**fájlt, és illessze be a Kubernetes-kapcsolat ablak. Válassza ki a**OK**gombra.

    ![Kubernetes Service-végpont](media\azure-stack-solution-machine-learning\image124.png)
    

3.  A Kubernetes-végpont létrehozása és kiválasztott, után jelölje használata konfigurációs fájlok be a konfigurációs fájl hozzáadásához. Majd keresse meg a csatolt összetevők iris_deployment.yaml fájlt.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image125.png)

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image126.png)

4.  A kiadási definíció mentéséhez.

#### <a name="check-the-status-of-the-release-definition"></a>A kiadási definíció állapotának ellenőrzéséhez. 

A mentés után a kiadási definíció a VSTS automatikusan elindíthat kell egy kiadást.

Gyors parancs futtatása a WSL parancssori ablakban, majd ellenőrzi a Kubernetes felhasználói felülete az üzembe helyezés állapotának ellenőrzéséhez.

```Bash  
kubectl get deployments
```

A kimenet a központi telepítés folyamatban, hasonlóan kell kinéznie.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

Ha a kubernetes felhasználói felület már fut, keresse meg a központi telepítésben [ **https://localhost:8001/** ](https://localhost:8001/) majd keresse meg a **számítási feladatok -> replikakészletekhez**.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>A YAML-szolgáltatás üzembe helyezése

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>Töltse fel a iris_service.yaml a tárházat és a szinkronizálási módosítások

1.  Navigáljon az újonnan klónozott tárház:

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image75.png)

1.  Másolás a **iris_service.yaml** fájlt a tárházba.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  Véglegesítse a módosítást a GITBEN

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>Frissítés a YAML-kódot készíthet definíciója

1.  Válassza ki a buildek szakasz alatt a Build és kiadás eseményközpont, és válassza ki a korábban létrehozott definíciót.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image130.png)

2.  Válassza ki a Szerkesztés gombra a definíció szerkesztése.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image131.png)

3.  **Feladat hozzáadása** fázisa. Válassza ki **összeállítása összetevők közzététele** a a **segédprogram** listájára és válassza ki **hozzáadása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image108.png)

4.  Nevezze el **a Kubectl a alkalmazni** (név), és válassza ki a alkalmaz parancsot.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>Frissítés a kiadási definíció a YAML-kódot

1.  Válassza ki a Build és kiadás központ alatt theReleases szakaszt, és válassza ki a korábban létrehozott kiadási definíció. Ezután válassza ki a Szerkesztés hivatkozásra.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image132.png)

1.  Válassza ki a környezetet **Azure Stack** majd egy új feladat hozzáadása az Azure Stackhez.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image133.png)

1.  Hozzáadás egy **új feladat** ebben a fázisban válassza a **üzembe helyezés, kubernetes** alatt feladat **telepítés** , és válassza ki **Hozzáadás**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image134.png)

1.  Nevezze el **a Kubectl a alkalmazni** (név), és válassza ki a alkalmaz parancsot.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image109.png)

1.  Állítsa be a Kubernates szolgáltatáskapcsolódási a korábban létrehozott Azure Stack-kapcsolatra, és válassza a **konfigurációs fájlok használata** jelölőnégyzetet, adjon hozzá egy konfigurációs fájlt. Keresse meg a csatolt összetevők iris_service.yaml fájlt.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image135.png)


    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image136.png)

1.  A kiadási definíció mentéséhez.

#### <a name="check-the-status-of-the-release-definition"></a>A kiadási definíció állapotának ellenőrzése

A mentés után a kiadási definíció a VSTS automatikusan elindíthat kell egy kiadást.

Gyors parancs futtatása a WSL parancssori ablakban, majd ellenőrzi a Kubernetes felhasználói felülete az üzembe helyezés állapotának ellenőrzéséhez.

```Bash  
kubectl get deployments
```

A kimenet a központi telepítés folyamatban, hasonlóan kell kinéznie.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

Ha a kubernetes felhasználói felület már fut, keresse meg a központi telepítésben [ **https://localhost:8001/** ](https://localhost:8001/) majd keresse meg a **számítási feladatok -> replikakészletekhez**.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Kubernetes pontozási és ellenőrzési

Indítsa el a Kubernetes felhasználói felülete

```Bash  
kubectl proxy
```

Keresse meg a Kubernetes felhasználói felülete, majd nyissa meg **központi telepítések** -> **Iris-üzembe helyezési** -> **új replikakészlet**  ->  **Iris-telepítés – xxxxxxxxx** (ahol az xs az üzemelő példány azonosítója).

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image138.png)

Majd keresse meg a **szolgáltatások** , és válassza ki a **külső végpont** a szolgáltatás ellenőrzése, hogy működik.

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image139.png)

Egy érvényesítési az alábbihoz hasonló üzenetnek kell megjelennie:

![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Hozzon létre az Azure Stack pontozási Függvényalkalmazást az Azure Stack portálon

Minden függvény végrehajtásának egy függvényalkalmazáshoz szükséges. A függvényalkalmazás lehetővé teszi, hogy egyszerűbb felügyeleti, üzembe helyezéséhez és erőforrás-megosztás egy logikai egységként csoportosítási funkció.

1.  Az Azure Stack felhasználói portálon, válassza ki a **+ új** majd válassza ki a bal felső sarkában található gomb**Web + mobil** >**Függvényalkalmazás**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image141.png)

1.  Adja a függvénynek **data-funkciók** és helyezze ugyanazt az erőforráscsoportot és a fennmaradó Machine Learning tartalom. Lehetővé teszik az eszköz automatikus létrehozása egy új app service-csomag felhasználásra és a korábban létrehozott alkalmazás tárolására szolgáló tárfiókot használni.

    ![Új függvényalkalmazás-beállítások megadása](media\azure-stack-solution-machine-learning\image142.png)

1.  Válassza ki**létrehozás**és a függvényalkalmazás üzembe helyezéséhez.

2.  Válassza ki az értesítési ikon a portál jobb felső sarkában, és tekintse meg a a**üzembe helyezés sikeres** üzenet.

    ![Új függvényalkalmazás-beállítások megadása](media\azure-stack-solution-machine-learning\image143.png)

1.  Válassza ki**erőforrás megnyitása** az új függvényalkalmazás megtekintéséhez.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image144.png)

1.  Hozzon létre egy új függvényt kiválasztásával **funkciók**, akkor a **+ új funkció** gombra.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image145.png)

1.  Válassza ki a HTTP-eseményindító

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image146.png)

1.  Válassza ki **C\#**  , a nyelv és a függvény neve: **clean-pontszám-data**, és állítsa be a jogosultsági szintet **névtelen**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image147.png)

1.  Másolás és beillesztés a példában a tartalmát a következő hibakóddal tiszta pontszám-adatok a függvénybe.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Funkciók ellenőrzése a Postman használatával

Annak érdekében, hogy meg van adva a Kbernetes és funkciók megfelelően használhatja az ingyenes alkalmazást Postman tesztelésére és érvényesítésére sémákat és funkciók. Ez a folyamat elindításához először néhány információt gyűjthet a Kubernetes-példányból.

1.  Keresse meg a Kubernetes felhasználói felülete, majd nyissa meg **központi telepítések** -> **Iris-üzembe helyezési** -> **új replikakészlet**  ->  **Iris-telepítés – xxxxxxxxx** (ahol az xs az üzemelő példány azonosítója)

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image138.png)

1.  Majd keresse meg a **szolgáltatások** , és másolja a **külső végpont**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image149.png)

1.  Töltse le és telepítse a Postman alkalmazást [Itt](https://www.getpostman.com/apps) szükség esetén.

2.  Jelentkezzen be a Postman alkalmazást, és zárja be az új fájl párbeszédpanelen.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image150.png)

1.  A postman-alkalmazáson belül válassza POST...

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image151.png)

1.  Illessze be a **külső végpont** URL-CÍMÉT az a postman alkalmazást a **kérés URL-címe** hozzáadása  **\\pontszám** ahogy az alábbi URL-címe végéhez.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image152.png)

1.  Válassza ki a **törzs** fülre, majd az adatok típusa **nyers**, majd **JSON**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image153.png)

1.  Egy webböngészőben nyissa meg **külső végpont**. Az URL-címet ad hozzá a következő **/swagger.json** Ez a telepítés tesztelésére szolgáltatások Swagger-fájl vezet.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image154.png)

1.  A példában szereplő másolja a **Swagger.JSON** fájlt.

2.  A Postman alkalmazást a illessze be a példában a bejegyzés törzse, majd válassza ki **küldése**. Azt adja vissza egy érték hasonlít az alábbi képen látható.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>7. lépés: Az Azure Stack tárfiókok és a tárolási üzenetsor létrehozása

Hozzon létre egy Azure Stack tárfiókok és a Storage-üzenetsor adatokat.

1.  Jelentkezzen be az Azure Stack felhasználói portálon. (Minden egyes Azure Stack rendelkezik egy egyedi portál URL-címe)

2.  Az Azure Stack felhasználói portál az bontsa ki a szolgáltatások a menü megnyitásához, majd válassza a bal oldali **minden szolgáltatás**. Görgessen le a **tárolási** válassza **tárfiókok**. Az a **Tárfiókok** ablakban válassza a **Hozzáadás**.

3.  Adja meg a tárfiók nevét.

4.  Válassza ki a tárfiók replikálási beállítását: **LRS**.

5.  Adjon meg egy új erőforráscsoportot, vagy válasszon ki egy meglévőt.

6.  Válassza ki **helyi** a tárfiók helyét.

7.  Válassza ki**létrehozás**a tárfiók létrehozásához.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image156.png)

1.  Válassza ki a nemrég létrehozott tárfiókot.

2.  Válassza ki a**üzenetsorok**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image157.png)

1.  Válassza ki a **+ üzenetsor** és nevezze el a várólista, és válasszon **OK.**

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image158.png)

1.  Első a **kapcsolati karakterlánc** számára a tárolási üzenetsort, és másolja azt.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image159.png)

1.  Keresse meg az Azure-Függvényalkalmazást, és válassza ki **Alkalmazásbeállítások**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image160.png)

1.  Az alkalmazás beállításait, a Függvényalkalmazás belül görgessen az alkalmazásbeállítások, és válassza **+ új beállítás hozzáadása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image161.png)

1.  Adja meg a storage-fiók nevét a **neve** mező, a teljes körű hozzáadása; tá_rolási

Ez lehetővé teszi az alkalmazás tudja, hogy ez az egy storage-fiók végpontját.

1.  Illessze be a kapcsolati karakterláncot a **érték** mező.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image162.png)

1.  Görgessen az alkalmazásbeállítások tetejére, és válassza ki **mentése**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>Tárolási üzenetsor használni kívánt pontozási függvény frissítése

1.  Válassza ki a **integrálása** a függvény, és törölje a kijelölését a **első** lehetőséget.

2.  Kattintson a **Mentés** gombra.

3.  Válassza ki **+ új kimenet** származó a kimenetek.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image164.png)

1.  Válassza ki **Azure Queue Storage** válassza **kiválasztása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image165.png)

1.  Frissítés a **üzenetsor neve** korábban létrehozott, és adja meg a Storage-üzenetsorhoz a **Tárfiók kapcsolata** létrehozott korábbi, és válassza ki a Tárfiók kapcsolata a **mentéséhez.**

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>8. lépés: Hozzon létre egy függvényt, amely kezeli az adatok tisztítása

Hozzon létre egy új Azure Stack-függvény a tiszta adatok áthelyezése az Azure Stack az Azure-bA.

1.  Hozzon létre egy új függvényt kiválasztásával **funkciók**, akkor a **+ új funkció** gombra.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image167.png)

1.  Válassza ki **időzítő eseményindító**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image168.png)

1.  Válassza ki **C\#**  , a nyelv és a függvény neve: **feltöltése – azure** , és állítsa az ütemezést **0 0 \*/1 \* \* \***  amely CRON jelöléssel van óránként egyszer.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Üzemeltetett Azure Storage-fiókhoz tartozó kapcsolati karakterlánc beolvasása

1.  Keresse meg a <https://portal.azure.com> majd válassza ki a **Azure Storage-fiók** korábban létrehozott.

2.  Válassza ki **hozzáférési kulcsok**, majd másolja a **kapcsolati karakterlánc** a tárfiókhoz.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>A feltöltés – azure használata az Azure üzemeltetett tárolási függvény frissítése

1.  Keresse meg az Azure-Függvényalkalmazást, és válassza ki **Alkalmazásbeállítások**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image171.png)

1.  Az alkalmazás beállításait, a Függvényalkalmazás belül görgessen az alkalmazásbeállítások, és válassza **+ új beállítás hozzáadása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image172.png)

1.  Adja meg a storage-fiók nevét a **neve** mező, a teljes körű hozzáadása; tá_rolási

Ez lehetővé teszi az alkalmazás tudja, hogy ez az egy storage-fiók végpontját.

1.  Illessze be az Azure szolgáltatott Tárfiók kapcsolati Sztringje be a **érték** mező.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image173.png)

1.  Görgessen az alkalmazásbeállítások tetejére, és válassza ki **mentése**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image174.png)

1.  Lépjen vissza a **feltöltése – azure** függvény.

2.  Válassza ki a **integrálása** a függvény.

3.  Válassza ki **+ új kimenet** származó a kimenetek.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image175.png)

1.  Válassza ki **Azure Blob Storage** válassza **kiválasztása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image176.png)

1.  Frissítés a **elérési** formátuma a következő korábban létrehozott storage-tárolóba: **uploadeddata / {rand-guid} .txt**, majd állítsa be a **Tárfiók kapcsolata** , a Tárfiók kapcsolata az Azure-ban létrehozott korábbi és select **mentéséhez.**

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image177.png)

1.  Másolás és beillesztés a példában a tartalmát a code **feltöltése – azure** a függvénybe.

2.  A Storage-fiókok kapcsolati karakterlánc átirányítása igény szerint módosítsa.

3.  Mentse és futtassa a kódot.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image178.png)

1.  Ellenőrizze, hogy az adatok Azure üzemeltetett Storage-fiókhoz tartozik elemzett akár a felhőben az Azure-ból: sikeres hasonlóan néz ki az alábbi.

    ![Helyettesítő szöveg](media\azure-stack-solution-machine-learning\image179.png)

Az adatok fertőtlenítsék a bizalmas adatok az Azure Stack üzemeltetett Kubernetes gépi tanulással és a helyszíni Azure Stack-n keresztül az Azure Stack üzemeltetett Function Apps, az Azure nyilvános felhőbe feltöltött és úgy végezheti el az adatokat egy edge/leválasztása a feltöltéshez Ez a forgatókönyv.

## <a name="next-steps"></a>További lépések

 - Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).