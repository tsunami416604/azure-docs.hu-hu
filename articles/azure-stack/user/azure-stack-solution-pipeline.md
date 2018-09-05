---
title: Alkalmazás üzembe helyezése az Azure és az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan telepíthet alkalmazásokat az Azure és az Azure Stack egy hibrid CI/CD-folyamat.
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
ms.date: 09/04/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 391cc4ca4b34149aeda54a60bfe6f6949e5a379b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697747"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Oktatóanyag: alkalmazások telepítése az Azure és az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Ismerje meg, hogyan helyezhet üzembe egy alkalmazást az Azure és az Azure Stack egy hibrid folyamatos integráció/folyamatos teljesítés (CI/CD) folyamat használatával.

Ebben az oktatóanyagban egy mintául szolgáló környezetet hozunk létre:

> [!div class="checklist"]
> * Kód véglegesítése a Visual Studio Team Services (VSTS) tárházhoz alapján új build kezdeményezni.
> * A felhasználói tesztelés az Azure globális üzembe helyezhetők az alkalmazást.
> * A kód továbbítja, tesztelés, üzembe helyezhetők az alkalmazás az Azure Stackhez.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>A hibrid kézbesítési előnyei cső létrehozása

Folytonosságát, biztonságot és megbízhatóságot alkalmazás központi telepítésének legfontosabb elemei. Ezek az elemek a következők: alapvető, a szervezet számára és a fejlesztői csapat számára kritikus. Egy hibrid CI/CD-folyamat lehetővé teszi a build csövek összevonni a helyszíni környezet és a nyilvános felhőben. A hibrid kézbesítési modell is lehetővé teszi a központi telepítés módosítása az alkalmazás módosítása nélkül.

Egyéb előnyei is a hibrid megközelítés a következők:

* A konzisztens fejlesztési eszközök akkor is fenntartható a helyszíni Azure Stack-környezet és az Azure nyilvános felhő között.  Eszköz tulajdonságkészlettel megkönnyíti a CI/CD patterns and practices nevű megvalósításához.
* Alkalmazások és szolgáltatások Azure-ban üzembe helyezett felcserélhető, és ugyanazt a kódot futtathatja az egyik helyen. A helyszíni és a nyilvánosfelhő-funkciók és képességek előnyeit is igénybe vehet.

További információ a CI és CD-ről további információt:

* [Mi a folyamatos integráció?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Mi a folyamatos teljesítés?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Előfeltételek

Hozhat létre egy hibrid CI/CD-folyamat összetevőit rendelkezniük kell. A következő összetevők előkészítése időt vesz igénybe:

* Egy Azure-beli OEM vagy hardveres partner üzembe helyezhet egy éles környezetben az Azure Stack. Összes felhasználó telepítheti az Azure Stack Development Kit (ASDK).
* Az Azure Stack-operátorokról kell is: az App Service üzembe helyezése, hozhat létre csomagokat és ajánlatokat, hozzon létre egy bérlői előfizetéshez, és a Windows Server 2016 rendszerképet.

>[!NOTE]
>Ha már rendelkezik ezeket az összetevőket telepített némelyikét, győződjön meg arról, hogy azok megfelelnek az összes Ez az oktatóanyag megkezdése előtt.

Ez az oktatóanyag feltételezi, hogy néhány alapvető ismeretek az Azure és az Azure Stack. Az oktatóanyag megkezdése előtt további tudnivalókért olvassa el a következő cikkeket:

* [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
* [Az Azure Stack főbb fogalmak](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Azure-követelmények

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Hozzon létre egy [Web App](https://docs.microsoft.com/azure/app-service/app-service-web-overview) az Azure-ban. Győződjön meg, vegye figyelembe a Web App URL-címének kell, hogy az oktatóanyag részében használni.

### <a name="azure-stack-requirements"></a>Azure Stack-követelmények

* Használja az Azure Stackkel integrált rendszer, vagy üzembe helyezése az Azure Stack Development Kit (ASDK). A ASDK telepítéséhez:
    * A [oktatóanyag: a telepítő a ASDK üzembe](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) részletes telepítési utasításokat biztosít.
    * Használja a [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) PowerShell-parancsprogram ASDK üzembe helyezés utáni lépések automatizálásához.

    > [!Note]
    > ASDK telepítése befejeződik, így ennek megfelelően tervezze körülbelül hét órát vesz igénybe.

 * Üzembe helyezése [App Service-ben](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-szolgáltatások az Azure Stackhez.
 * Hozzon létre [csomag/ajánlatok](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) az Azure Stackben.
 * Hozzon létre egy [bérlői előfizetéshez](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) az Azure Stackben.
 * Webalkalmazás létrehozása a bérlői előfizetéshez. Jegyezze fel az új webes alkalmazás URL-CÍMÉT a későbbi használatra.
 * A bérlő előfizetés VSTS virtuális gép üzembe helyezése.
* Adja meg a .NET 3.5 a Windows Server 2016 rendszerképet a virtuális gép (VM). Ez a virtuális gép, egy privát fordító-ügynökhöz az Azure Stack lesz felépítve.

### <a name="developer-tool-requirements"></a>Fejlesztői eszköz követelményei

* Hozzon létre egy [VSTS munkaterület](https://docs.microsoft.com/vsts/repos/tfvc/create-work-workspaces). A regisztrációs folyamat hoz létre a projekt neve **MyFirstProject**.
* [Visual Studio 2017 telepítése](https://docs.microsoft.com/visualstudio/install/install-visual-studio) és [jelentkezzen be a vsts-ben](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Csatlakozás a projekthez, és [helyben klónozhatja](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Az Azure Stack környezettel kell a megfelelő rendszerképek hírcsatorna-a Windows Server és SQL Server futtatásához. App Service-ben üzembe helyezett is kell rendelkeznie.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Visual Studio Team Services-integráció előkészítését a saját build és kiadás ügynök

### <a name="prerequisites"></a>Előfeltételek

Visual Studio Team Services (VSTS) hitelesíti, szemben az Azure Resource Manager használatával egy egyszerű szolgáltatást. VSTS kell rendelkeznie a **közreműködői** szerepkör erőforrások kiépítése az Azure Stack-előfizetéshez.

Az alábbi lépések bemutatják, hogy mi szükséges hitelesítés konfigurálása:

1. Hozzon létre egy egyszerű szolgáltatást, vagy használjon egy meglévő egyszerű szolgáltatást.
2. Hitelesítési kulcsok számára az egyszerű szolgáltatás létrehozása.
3. Ellenőrizze az Azure Stack-előfizetéshez szerepköralapú hozzáférés-vezérlést, hogy a egyszerű szolgáltatásnév (SPN), a közreműködő szerepkör részét.
4. A vsts-ben az Azure Stack-végpontok és egyszerű szolgáltatásnév segítségével, hozzon létre egy új szolgáltatás definíciójában.

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Tekintse meg a [egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) utasításokat az egyszerű szolgáltatás létrehozásához. Válasszon **Web App és az API** az alkalmazás típusához vagy [a PowerShell-szkripttel](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) a cikkben leírtak szerint [hozzon létre egy meglévő szolgáltatás Azure Resource Manager-szolgáltatás kapcsolatot egyszerű ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Ha a parancsfájl használatával hozzon létre egy Azure Stack az Azure Resource Manager-végpontot, teljesítenie kell a a **- azureStackManagementURL** paraméter és **- environmentName** paraméter. Példa:  
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Hozzáférési kulcs létrehozása

Egy egyszerű szolgáltatást a hitelesítési kulcs szükséges. Az alábbi lépések segítségével hozzon létre egy kulcsot.

1. Válassza ki az alkalmazást az Azure Active Directory **Alkalmazásregisztrációk** területén.

    ![Válassza ki az alkalmazást](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Jegyezze fel az értékét **Alkalmazásazonosító**. Ezt az értéket fogja használni, amikor a szolgáltatásvégpont beállítása a vsts-ben.

    ![Alkalmazásazonosító](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. A hitelesítési kulcs létrehozásához válassza a **Beállítások** elemet.

    ![Alkalmazások beállításainak szerkesztése](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

    ![Számítógépkulcs beállításainak konfigurálása](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Adjon meg egy leírást a kulcshoz, és a kulcs időtartam beállítása. Ha elkészült, kattintson a **Mentés** elemre.

    ![Kulcs leírása és időtartama](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    A kulcs, a kulcs mentése után **érték** jelenik meg. Másolja ezt az értéket, mert ezt az értéket később nem kapott. Azt adja meg a **kulcs értékét** jelentkezzen be az alkalmazást, hogy az alkalmazás azonosítójával. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

    ![A kulcs értéke](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>A Bérlőazonosító beszerzése

A Szolgáltatásvégpontok konfigurálásának részeként a vsts-ben van szükség a **Bérlőazonosító** , amely megfelel az AAD-címtárában, amelyet az Azure Stack stamp. Kövesse az alábbi lépéseket lekérni a bérlő azonosítóját.

1. Válassza az **Azure Active Directory** elemet.

    ![Az Azure Active Directory-bérlő](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. A bérlőazonosító lekéréséhez válassza ki az Azure AD-bérlőjéhez tartozó **Tulajdonságok** elemet.

    ![Bérlő tulajdonságainak megtekintése](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Másolja ki a **Címtár-azonosítót**. Ez az érték a bérlőazonosítója.

    ![Címtár azonosítója](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>A szolgáltatás egyszerű engedélyeket az Azure Stack-előfizetéshez erőforrásokat helyezhet üzembe

Az előfizetésben lévő erőforrások eléréséhez, hozzá kell rendelnie az alkalmazás egy szerepkörhöz. Döntse el, melyik szerepkör jelöli az ajánlott engedélyeket az alkalmazáshoz. Az elérhető szerepkörök kapcsolatos további információkért lásd: [RBAC: beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Beállíthatja a hatókör szintjén is az előfizetés, erőforráscsoport vagy erőforrás. Alacsonyabb szintű hatókör, az engedélyek öröklődnek. Például egy alkalmazás az Olvasó szerepkörhöz, egy erőforráscsoport hozzáadása azt jelenti, hogy azt az erőforráscsoportot és a hozzá tartozó erőforrások olvashatja.

1. Keresse meg a hatókör az alkalmazást hozzárendelni kívánt szintjét. Válassza ki például az előfizetések szintjén szerepkör hozzárendelése **előfizetések**.

    ![Előfizetések kiválasztása](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. A **előfizetés**, válassza ki a Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Válassza ki a Visual Studio Enterprise **hozzáférés-vezérlés (IAM)**.

    ![Hozzáférés-vezérlés (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Válassza a **Hozzáadás** lehetőséget.

    ![Hozzáadás](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. A **engedélyek hozzáadása**, válassza ki a szerepkört, hogy az alkalmazáshoz hozzárendelni kívánt. Ebben a példában a **tulajdonosa** szerepkör.

    ![Tulajdonosi szerepkör](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Alapértelmezés szerint az Azure Active Directory-alkalmazások nem megjelenik az elérhető lehetőségek közül. Keresse meg az alkalmazást, meg kell adnia annak nevét a a **kiválasztása** mezőt a keresési funkciót. Válassza ki az alkalmazást.

    ![Alkalmazás keresési eredmény](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Válassza ki **mentése** befejeződik, a szerepkör hozzárendelése. Láthatja, hogy az alkalmazás a felhasználók az adott hatókörnél szerepköre a listában.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáférés-vezérlést az Azure biztosít. Az RBAC használatával szabályozhatja, amelyek a feladataik elvégzéséhez szükséges felhasználói hozzáférési szintjét. Szerepköralapú hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Azure-előfizetések erőforrásaihoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>VSTS-Ügynökkészletek

Ahelyett, hogy minden ügynök külön-külön, ügynökök is szervezheti ügynökkészletek. Az ügynökkészlet a megosztási határt a készlet az összes ügynöknek határozza meg. A vsts-ben a VSTS-fiók, ami azt jelenti, hogy különböző csapatprojektek megoszthatja az ügynökkészlet ügynökkészletek hatóköre. Ügynökkészletek kapcsolatos további információkért lásd: [Ügynökkészletek létrehozása és az üzenetsorok](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adjon hozzá egy személyes hozzáférési jogkivonat (PAT) az Azure Stackhez

Hozzon létre egy személyes hozzáférési tokent VSTS eléréséhez.

1. Jelentkezzen be a VSTS-fiók, és válassza ki a profil nevét.
2. Válassza ki **biztonság kezeléséhez** hozzáférési token létrehozása oldalra.

    ![Felhasználói bejelentkezés](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Válassza ki a csapatprojekt](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Személyes hozzáférési jogkivonat hozzáadása](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Token létrehozása](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Másolja a jogkivonatot.

    > [!Note]
    > Mentse a token információkat. Ezt az információt nem tárolja, és nem jelennek meg újra amikor hagyja meg a weblapot.

    ![Személyes hozzáférési jogkivonat](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Telepítse a VSTS ügynök létrehozása az Azure Stack-kiszolgálón üzemeltetett kiszolgáló létrehozása

1. Csatlakozzon a hozhat létre, az Azure Stack gazdagépen telepített kiszolgáló.
2. Letöltése és telepítése a fordító-ügynökhöz szolgáltatás használata a személyes hozzáférési jogkivonat (PAT), és a virtuális gép rendszergazdai fiókot használnia.

    ![A build-ügynök letöltése](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Keresse meg azt a mappát a kibontott fordító-ügynökhöz. Futtassa a **config.cmd** fájl egy rendszergazda jogú parancssorból.

    ![Kivont fordító-ügynökhöz](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Regisztrálja a fordító-ügynökhöz](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Amikor befejezi a config.cmd, további fájlok frissül a build-ügynök mappájába. A mappát a kibontott tartalma a következőhöz hasonlóan kell kinéznie:

    ![Az ügynök mappa frissítés létrehozása](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Az ügynök VSTS mappában tekintheti meg.

## <a name="endpoint-creation-permissions"></a>Végpont létrehozása engedélyek

Végpontok létrehozása a Visual Studio online-hoz (VSTO) build helyezzen üzembe Azure Service-alkalmazások az Azure Stackhez. Vsts-ben a fordító-ügynökhöz, amely csatlakoztatja az Azure Stackhez csatlakozik.

![A VSTO NorthwindCloud mintaalkalmazás](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Jelentkezzen be a VSTO, és nyissa meg az alkalmazás beállítások oldalát.
2. A **beállítások**válassza **biztonsági**.
3. A **VSTS csoportok**válassza **végpont alkotói**.

    ![A létrehozók NorthwindCloud végpont](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Az a **tagok** lapon jelölje be **Hozzáadás**.

    ![Tag hozzáadása](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. A **felhasználók és csoportok hozzáadása**, írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a felhasználók listájából.
6. Válassza ki **módosítások mentése**.
7. Az a **VSTS csoportok** listáról válassza ki **végpont rendszergazdák**.

    ![A rendszergazdák NorthwindCloud végpont](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Az a **tagok** lapon jelölje be **Hozzáadás**.
9. A **felhasználók és csoportok hozzáadása**, írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a felhasználók listájából.
10. Válassza ki **módosítások mentése**.

## <a name="create-an-azure-stack-endpoint"></a>Az Azure Stack-végpont létrehozása

Az utasítások a [kapcsolatot hozhat létre egy Azure Resource Manager szolgáltatás egy meglévő szolgáltatás egyszerű ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) hozzon létre egy szolgáltatás-kapcsolatot egy meglévő szolgáltatás egyszerű, és használja a következő társítás a cikk:

- Környezet: AzureStack
- Környezet URL-címe: Hasonló dolgot `https://management.local.azurestack.external`
- Előfizetés azonosítója: Felhasználói előfizetés-azonosító az Azure Stackben
- Előfizetés neve: az Azure Stack felhasználói előfizetés neve
- Egyszerű szolgáltatás ügyfél-azonosító: a résztvevő-Azonosítóval [ez](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#create-a-service-principal) szakasz ebben a cikkben.
- Egyszerű szolgáltatásnév kulcsát: A kulcsát, az ugyanebben a cikkben (vagy a jelszót, ha a szkriptet használta).
- Bérlőazonosító: A bérlő Azonosítóját kérheti le az utasítás a következő [a Bérlőazonosító beszerzése](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id).

Most, hogy a végpont a létrehozás, a vsts-ben az Azure Stack kapcsolat készen áll a használatra. A fordító-ügynökhöz az Azure Stackben utasításokat lekérdezi a VSTS-ből, és ezután az ügynök közvetíti a kommunikációt az Azure Stack-végpontjának információit.

![Az ügynök létrehozása](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Fejlesztés a sestavení aplikace

Az oktatóanyag ezen részében jelennek meg:

* Adja hozzá a kódot egy VSTS-projekthez.
* Hozzon létre önálló webes alkalmazás üzembe helyezése.
* A folyamatos üzembe helyezési folyamat beállítása

> [!Note]
 > Az Azure Stack környezettel kell a megfelelő rendszerképek hírcsatorna-a Windows Server és SQL Server futtatásához. App Service-ben üzembe helyezett is kell rendelkeznie. Tekintse meg az App Service-ben dokumentációt "Előfeltételek" szakaszban az Azure Stack-kezelők követelményeinek.

CI/CD hibrid alkalmazás kódja és az infrastruktúra kódjának alkalmazhatja. Használat [Azure Resource Manager-sablonokat, mint webes ](https://azure.microsoft.com/resources/templates/) alkalmazáskód mindkét felhőben való üzembe helyezése a VSTS-ből.

### <a name="add-code-to-a-vsts-project"></a>Adja hozzá a kódot egy VSTS-projektbe

1. Jelentkezzen be a VSTS-fiókkal rendelkező projekt létrehozása az Azure Stacken. A következő képernyőfelvétel bemutatja, hogyan kapcsolódjon a HybridCICD.

    ![Csatlakozás a projekthez](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **A tárház klónozása** létrehozásával és az alapértelmezett webes alkalmazás megnyitásával.

    ![Klónozott tárház](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Önálló webes alkalmazás üzembe helyezése az App Services létrehozása az mindkét felhőben

1. Szerkesztés a **WebApplication.csproj** fájl: válassza ki **Runtimeidentifier** majd `win10-x64.` további információkért lásd: [önálló központi telepítés](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentáció.

    ![Runtimeidentifier konfigurálása](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Team Explorer segítségével ellenőrizze a kódot a VSTS-be.

3. Győződjön meg arról, hogy az alkalmazás kódja Visual Studio Team Services-be lett-e érvényesítve.

### <a name="create-the-build-definition"></a>A build definíció létrehozása

1. Jelentkezzen be a VSTS-fiókkal, amely a builddefiníció hozhat létre.
2. Keresse meg a **webes alkalmazás készítése** a projekt lapját.

3. A **argumentumok**, adjon hozzá **- r win10-x64** kódot. Ez egy önálló telepítés a .NET használatával aktiválásához szükséges alapvető.

    ![Argument builddefiníció hozzáadása](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Futtassa a build. A [önálló telepítés build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat közzéteszi az összetevők, amelyek futhatnak az Azure és az Azure Stackben.

### <a name="use-an-azure-hosted-build-agent"></a>Azure-beli használatra üzemeltetett fordító-ügynökhöz

A vsts-ben üzemeltetett build ügynök használatával lehetőség egy kényelmes és webes alkalmazások üzembe. Ügynök karbantartása és frissítései automatikusan végzi a Microsoft Azure, amely lehetővé teszi a folyamatos és zavartalan fejlesztési ciklus.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurálja a folyamatos készregyártás (CD) folyamatot

Visual Studio Team Services (VSTS) és a Team Foundation Server (TFS) adja meg hatékonyan konfigurálható és kezelhető folyamat számára több környezetekben, például fejlesztési, előkészítési, minőségbiztosítási (Tesztelés), és éles környezetben. Ez a folyamat tartalmazhat jóváhagyásokat igénylő az alkalmazás életciklusát az adott szakaszában.

### <a name="create-release-definition"></a>Kiadási definíció létrehozása

Kiadási definíció létrehozása az utolsó lépés az alkalmazás létrehozása folyamatban. A kiadási definíció kiadás létrehozása és üzembe helyezése a build szolgál.

1. Jelentkezzen be a vsts-ben, és navigáljon a **készítése és kiadása** a projekthez.
2. Az a **kiadásokban** lapon jelölje be  **\[ +]** majd válasszon **kiadási definíció létrehozása**.

   ![Kiadási definíció létrehozása](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. A **válasszon ki egy sablont**, válassza a **Azure App Service üzembe helyezési**, majd válassza ki **alkalmaz**.

    ![Sablon telepítése](media\azure-stack-solution-hybrid-pipeline\102.png)

4. A **Hozzáadás összetevő**, az a **forrás (builddefiníció)** legördülő menüben válassza ki az Azure Cloud hozhat létre alkalmazást.

    ![Összetevő hozzáadása](media\azure-stack-solution-hybrid-pipeline\103.png)

5. Az a **folyamat** lapon jelölje be a **1. fázis**, **1 feladat** mutató hivatkozás **környezettel kapcsolatos feladatok megtekintéséhez**.

    ![Folyamat nézet feladatai](media\azure-stack-solution-hybrid-pipeline\104.png)

6. Az a **feladatok** lapra, adja meg az Azure-t a **környezet neve** , és válassza ki az AzureCloud Traders-webalkalmazás EP a **Azure-előfizetés** legördülő listából válassza ki.

    ![Környezeti változók beállítása](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Adja meg a **az Azure app service neve**, azaz a következő képernyőfelvétel-készítés "northwindtraders".

    ![App service neve](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Válassza ki az ügynök fázis **Hosted VS2017** származó a **fronta Agenta** legördülő listából válassza ki.

    ![Üzemeltetett ügynök](media\azure-stack-solution-hybrid-pipeline\107.png)

9. A **üzembe helyezése az Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezethez.

    ![Csomag vagy mappa kiválasztása](media\azure-stack-solution-hybrid-pipeline\108.png)

10. A **válassza ki a fájl vagy mappa**válassza **OK** való **hely**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Mentse az összes módosítást, és térjen vissza a **folyamat**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\110.png)

12. A a **folyamat** lapon jelölje be **Hozzáadás összetevő**, és válassza a **NorthwindCloud Traders-hajó** a a **Source (Build definíció)** legördülő listából.

    ![Új összetevő hozzáadása](media\azure-stack-solution-hybrid-pipeline\111.png)

13. A **válasszon ki egy sablont**, adjon hozzá egy másik környezetre. Válasszon **Azure App Service üzembe helyezési** majd **alkalmaz**.

    ![Sablon kiválasztása](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Adja meg az "Azure Stack" a **környezetnevet**.

    ![Környezet neve](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Az a **feladatok** lapon keresse meg és válassza ki az Azure Stack.

    ![Az Azure Stack-környezet](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Az a **Azure-előfizetés** legördülő listára, válassza ki a "AzureStack Traders-hajó EP" az Azure Stack-végpont.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Adja meg az Azure Stack webes alkalmazás nevét, a **App service neve**.

    ![App service neve](media\azure-stack-solution-hybrid-pipeline\116.png)

18. A **kijelölése**, "AzureStack - bDouglas részéhez" választhatok a **fronta Agenta** legördülő listából.

    ![Az ügynök kiválasztása](media\azure-stack-solution-hybrid-pipeline\117.png)

19. A **üzembe helyezése az Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezethez. A **fájl vagy mappa kiválasztása**válassza **OK** mappa **hely**.

    ![Csomag vagy mappa kiválasztása](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Hely jóváhagyása](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Az a **változó** lapra, keresse meg a következő változót **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. A változó értékét állítsa **igaz**, és állítsa annak hatókörét **Azure Stack**.

    ![A változó konfigurálása](media\azure-stack-solution-hybrid-pipeline\120.png)

21. Az a **folyamat** lapon jelölje be a **a folyamatos készregyártás eseményindítója** ikonjára a NorthwindCloud Traders-webalkalmazás-összetevő és a készlet a **a folyamatos készregyártás eseményindítója** , **Engedélyezve**.  Járjon el ugyanígy "NorthwindCloud Traders-hajó" összetevőre.

    ![A folyamatos készregyártás eseményindítója beállítása](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Az Azure Stack-környezet, válassza a **központi telepítés előtti feltételek** ikon beállítani az eseményindítót **kiadás után**.

    ![Beállítása feltételek a központi telepítés előtti eseményindító](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Mentse az összes módosítást.

> [!Note]
> Néhány beállítás, a feladatkiadási tevékenységeket előfordulhat, hogy automatikusan meghatározott [környezeti változók](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) létrehozásakor, a kiadási definíció a sablon alapján. Ezek a beállítások nem módosíthatók az a tevékenység beállításait. Azonban ezeket a beállításokat a környezetben a szülőelemektől szerkesztheti.

## <a name="create-a-release"></a>Hozzon létre egy kiadás

Most, hogy végrehajtotta a kiadási definíció módosításait, azt az idő a üzembe helyezésének megkezdéséhez. Ehhez hozzon létre egy a kiadási definíció a kiadásra. A kiadás automatikusan; előfordulhat, hogy hozható létre Ha például a folyamatos készregyártás eseményindítója van megadva a kiadási definíció. Ez azt jelenti, hogy a Forráskód módosítása elindul egy új létrehozást és az új kiadás. Azonban ebben a szakaszban létrehoz egy új kiadásban manuálisan.

1. Az a **folyamat** lap meg van nyitva a **kiadási** legördülő listában, és válassza a **kiadás létrehozása**.

    ![Hozzon létre egy kiadás](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Adjon meg egy leírást a kiadás, ellenőrizze, hogy, hogy a helyes összetevők vannak-e jelölve, és válassza **létrehozás**. Néhány pillanat múlva egy szalagcím jelenik meg, amely azt jelzi, hogy az új kiadásban lett létrehozva, és a kiadási név hivatkozásként jelenik meg. Válassza ki a hivatkozásra kattintva megtekintheti a kiadási összegző lapja.

    ![Kiadás létrehozása szalagcím](media\azure-stack-solution-hybrid-pipeline\201.png)

3. A kiadási összefoglaló lapja a kiadással kapcsolatos részleteket jeleníti meg. "Release-2", az alábbi képernyőfelvételen a **környezetek** szakasz látható a **központi telepítési állapot** esetében az Azure-t a "Folyamatban", és az Azure Stack állapota "sikeres". Ha az Azure-környezetre vonatkozó üzembe helyezési állapotra vált, "SUCCEEDED", egy szalagcím jelenik meg arról, hogy a kiadás nem áll készen a jóváhagyásra. Amikor a központi telepítés függőben, vagy sikertelen volt, a kék **(i)** információs ikon jelenik meg. A kurzort a az ikonra kattintva megtekintheti a egy előugró ablak, amely tartalmazza a késleltetés vagy a hiba okát.

    ![Kiadási összegző lapja](media\azure-stack-solution-hybrid-pipeline\202.png)

Más nézetekhez, például a listát a kiadások, is látható ikon jelzi, a jóváhagyási folyamatban. Erre az ikonra az előugró a környezet nevét, és üzembe helyezésével kapcsolatos további részleteket jeleníti meg. Könnyebbé vált a rendszergazda, lásd: a kiadások és hogy mely kiadásai jóváhagyásra várnak összesített állapotát.

### <a name="monitor-and-track-deployments"></a>A figyelő és nyomon követésére központi telepítések

Ez a szakasz bemutatja, hogyan figyelheti és nyomon követheti a központi telepítések. A kiadás a két Azure App Services-webhelyek üzembe jó példa biztosít.

1. A "Release-2" összesítő lapon válassza ki a **naplók**. A telepítés során ezen a lapon látható az élő napló az ügynöktől. A bal oldali panelen az egyes környezetekhez a központi telepítésben lévő egyes műveletek állapotát jeleníti meg.

    Kiválaszthatja, hogy egy személy ikonra a **művelet** oszlop ki a központi telepítés jóváhagyása (vagy elutasítása), és azokat a megadott üzenet központi telepítés előtti vagy utáni jóváhagyásra.

2. Az üzembe helyezés befejezését követően a teljes log fájl a jobb oldali ablaktáblán jelenik meg. Kiválaszthatja az esetleges **lépés** a naplófájl az egyetlen lépésben, például a "Feladat inicializálása" megtekintéséhez a bal oldali panelen. Egyéni naplók megtekinthetik megkönnyíti a követése és hibakeresése az általános üzembe helyezési részeit. Emellett **mentése** egy lépést, a naplófájl vagy **az összes napló letöltése zip-fájlként**.

    ![Kiadási naplók](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Nyissa meg a **összefoglalás** lapján megtekintheti a kiadással kapcsolatos általános információkat. Ez a nézet a build, a környezetekben, telepítve van, üzembe helyezési állapota és egyéb adatainak a kiadása kapcsolatos részleteket jeleníti meg.

4. Jelöljön ki egy környezetet hivatkozást (**Azure** vagy **Azure Stack**) és a egy adott környezetben való központi telepítés meglévő kapcsolatos információk megtekintéséhez. Ezek a nézetek, győződjön meg arról, hogy az azonos buildjét mindkét környezetben telepített egyszerűen használhatja.

5. Nyissa meg a **éles-WebApp üzembe helyezését** a böngészőben. Ha például az Azure App Services-webhely URL-címre `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>További lépések

* Az Azure-minták Felhőkhöz kapcsolatos további információkért lásd: [tervezési minták Felhőkhöz](https://docs.microsoft.com/azure/architecture/patterns).
