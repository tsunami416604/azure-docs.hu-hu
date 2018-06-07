---
title: Telepítse az alkalmazást az Azure és az Azure verem |} Microsoft Docs
description: Megtudhatja, hogyan telepíthet alkalmazásokat az Azure és az Azure-verem és a hibrid CI/CD.
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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604352"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Oktatóanyag: Azure és az Azure-verem alkalmazások telepítése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Útmutató: az alkalmazás üzembe helyezése Azure és a hibrid folyamatos integráció/folyamatos kézbesítési (CI/CD) kimenetátirányításának segítségével Azure verem.

Ebben az oktatóprogramban létre fog hozni egy minta környezetben:

> [!div class="checklist"]
> * Indítsa el a kód véglegesíti a Visual Studio Team Services (VSTS) tárházhoz alapján új buildverziót.
> * Az alkalmazás automatikus telepítése a felhasználói tesztelés globális Azure-bA.
> * Amikor a kód sikeres tesztelését, az alkalmazás automatikus telepítése Azure verem.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>A hibrid kézbesítési előnyei cső létrehozása

Folytonosságát, biztonságot és megbízhatóságot alkalmazás központi telepítésének legfontosabb elemei. Ezek az elemek a következők: alapvető fontosságú a szervezet és a fejlesztői csapat kritikus fontosságú. A hibrid CI/CD csővezeték lehetővé teszi a build csövek összevonni a helyszíni környezetben és a nyilvános felhő között. A hibrid kézbesítési modell emellett lehetővé teszi a központi telepítési helyének módosítása az alkalmazás módosítása nélkül.

A hibrid megközelítéssel egyéb előnyei a következők:

* A helyszíni Azure verem környezettől és az Azure nyilvános felhőjében keresztül akkor is fenntartható a fejlesztői eszközök konzisztens.  Egy általános eszköz-készlet megkönnyíti a CI/CD minták és gyakorlatok végrehajtásához.
* Az alkalmazások és szolgáltatások Azure vagy az Azure-verem telepítve felcserélhető, és ugyanazt a kódot futtathatja az egyik helyen. Kihasználhatja a helyszíni és nyilvános felhők szolgáltatásait és képességeit.

További információ a CI és CD-ről további:

* [Mi az a folyamatos integrációt?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Mi az a folyamatos kézbesítési?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Előfeltételek

Kell egy hibrid CI/CD folyamat build összetevők rendelkezik. A következő összetevők előkészítéséhez időt vesz igénybe:

* Egy Azure OEM-hardver partner telepítheti egy éles Azure verem. Összes felhasználó telepítheti az Azure verem Development Kit (ASDK).
* Egy Azure verem operátort kell is: az App Service telepítése, tervek és ajánlatok létrehozása, bérlő előfizetés létrehozása és a Windows Server 2016-lemezkép hozzáadása.

>[!NOTE]
>Ha már van egy telepített összetevők, győződjön meg arról, hogy azok megfelelnek az összes Ez az oktatóanyag elindítása előtt.

Ez az oktatóanyag feltételezi, hogy rendelkezik-e bizonyos Azure és az Azure-verem alapszintű ismeretét. Az oktatóanyag elindítása előtt további tudnivalókért olvassa el a következő cikkeket:

* [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
* [Az Azure verem kulcsfogalmak](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Azure-követelmények

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* Hozzon létre egy [webalkalmazás](https://docs.microsoft.com/azure/app-service/app-service-web-overview) az Azure-ban. Vegye figyelembe a webes alkalmazás URL-címének, meg kell használni az oktatóanyag.

### <a name="azure-stack-requirements"></a>Azure verem követelmények

* Integrált Azure verem rendszert használnak, vagy telepítse a Azure verem Development Kit (ASDK). A ASDK telepítéséhez:
    * A [oktatóanyag: a telepítő használatával ASDK telepítése](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) ad részletes utasításokat.
    * Használja a [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) ASDK telepítés utáni lépések automatizálásához PowerShell-parancsfájlt.

    > [!Note]
    > A ASDK telepítés befejeződik, ezért ennek megfelelően tervezheti körülbelül hét óráig tart.

 * Telepítése [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure verem PaaS-szolgáltatások.
 * Hozzon létre [terv/ajánlatok](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure navigációs veremben.
 * Hozzon létre egy [bérlői előfizetéshez](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure-készletben.
 * Webalkalmazás létrehozása a bérlői előfizetéshez. Jegyezze fel az új webes alkalmazás URL-CÍMÉT a későbbiekben használhassa.
 * A bérlő előfizetés VSTS virtuális gép üzembe helyezéséhez.
* Adja meg a Windows Server 2016-lemezkép a .NET 3.5-ös verzióját a virtuális gép (VM). A virtuális gép az Azure-veremben, személyes build megbízottként épül.

### <a name="developer-tool-requirements"></a>Fejlesztői eszköz követelmények

* Hozzon létre egy [VSTS munkaterület](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). A regisztrációs folyamat létrehoz egy nevű projekt **MyFirstProject**.
* [Telepítse a Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) és [bejelentkezés az VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Csatlakozzon a projekthez és [helyileg klónozza](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > A Windows Server és SQL Server futtatásához hírcsatorna megfelelő képek kell azt az Azure verem környezetet. App Service telepített is kell rendelkeznie.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Az a Visual Studio Team Services integráció előkészítését a titkos build és a kiadási ügynök

### <a name="prerequisites"></a>Előfeltételek

A Visual Studio Team Services (VSTS) szemben Azure Resource Manager használata egyszerű szolgáltatás hitelesíti. VSTS kell rendelkeznie a **közreműködő** egy Azure verem előfizetésben szereplő erőforrások kiépítése szerepkört.

A következő lépések bemutatják, mi szükséges hitelesítés konfigurálása:

1. Hozzon létre egy egyszerű szolgáltatást, vagy használjon egy meglévő egyszerű szolgáltatást.
2. A szolgáltatás egyszerű hitelesítés-kulcsok létrehozása.
3. A szerepköralapú hozzáférés-vezérlés engedélyezi az egyszerű szolgáltatásnév (SPN) a közreműködői szerepkör részeként Azure verem előfizetésébe ellenőrzése.
4. Hozzon létre egy új szolgáltatásdefiníció VSTS Azure verem végpontok és SPN információival.

### <a name="create-a-service-principal"></a>Egy egyszerű szolgáltatás létrehozása

Tekintse meg a [egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) utasításokat követve hozzon létre egy egyszerű szolgáltatást, és válassza a **Web App/API** az alkalmazás típusra.

### <a name="create-an-access-key"></a>Hozzáférési kulcs létrehozása

A szolgáltatás egyszerű hitelesítéshez kulcs szükséges. Az alábbi lépések segítségével hozzon létre egy kulcsot.

1. Válassza ki az alkalmazást az Azure Active Directory **Alkalmazásregisztrációk** területén.

    ![Válassza ki az alkalmazást](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Jegyezze fel a értékének **Alkalmazásazonosító**. Ezt az értéket fogja használni, amikor a szolgáltatási végpont konfigurálása a VSTS.

    ![Alkalmazásazonosító](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. A hitelesítési kulcs létrehozásához válassza a **Beállítások** elemet.

    ![Alkalmazásbeállítások szerkesztése](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

    ![Számítógépkulcs beállításainak konfigurálása](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Adjon meg egy leírást a kulcsot, és a kulcs időtartamának beállítása. Ha elkészült, kattintson a **Mentés** elemre.

    ![Kulcs leírását és időtartama](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    A kulcs, a kulcs mentése után **érték** jelenik meg. Másolja ezt az értéket, mert később nem érheti el ezt az értéket. Megadja a **kulcs értékét** az alkalmazás azonosítójával jelentkezzen be az alkalmazás számára. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

    ![A kulcs értéke](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>A-bérlőazonosító beszerzése

A végpont-konfiguráció részeként VSTS igényel a **Bérlőazonosító** , amely megfelel az AAD-címtárában, amely az Azure-verem stamp a rendszer. Az alábbi lépésekkel lekérni a bérlői azonosító.

1. Válassza az **Azure Active Directory** elemet.

    ![Az Azure Active Directory-bérlő](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. A bérlőazonosító lekéréséhez válassza ki az Azure AD-bérlőjéhez tartozó **Tulajdonságok** elemet.

    ![Bérlői tulajdonságainak megtekintése](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Másolja ki a **Címtár-azonosítót**. Ez az érték a bérlőazonosítója.

    ![Címtár azonosítója](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>A szolgáltatás egyszerű engedélyeket az Azure-verem előfizetés erőforrások telepítése

Az előfizetés az erőforrások eléréséhez az alkalmazást egy szerepkörhöz kell rendelni. Döntse el, melyik szerepkört jelöli az alkalmazás legjobb engedélyeit. A rendelkezésre álló szerepkörökkel kapcsolatos további tudnivalókért lásd: [RBAC: beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

A hatókör szintjén található az előfizetés, erőforráscsoportból vagy erőforrás állíthatja be. Engedélyek hatókör alacsonyabb szintre származnak. Például egy alkalmazást az olvasó szerepkört erőforráscsoport hozzáadása azt jelenti, hogy ez az erőforráscsoport és az erőforrásokat tudja olvasni.

1. Nyissa meg a szint szeretne hozzárendelni az alkalmazást. Például egy szerepkört az előfizetés hatókörből rendeléséhez jelölje **előfizetések**.

    ![Az előfizetések kiválasztása](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. A **előfizetés**, válassza ki a Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Válassza ki a Visual Studio Enterprise **hozzáférés-vezérlés (IAM)**.

    ![Hozzáférés-vezérlés (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Válassza a **Hozzáadás** lehetőséget.

    ![Hozzáadás](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. A **engedélyek hozzáadása**, válassza ki a szerepkört, hogy az alkalmazáshoz hozzárendelni kívánt. Ebben a példában a **tulajdonos** szerepkör.

    ![Tulajdonosi szerepkör](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Alapértelmezés szerint Azure Active Directory-alkalmazások nem jelennek meg a rendelkezésre álló beállításokat. Az alkalmazás található, meg kell adnia annak nevét a a **válasszon** mezővel rákereshet az azt. Válassza ki az alkalmazást.

    ![Alkalmazás keresési eredmény](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Válassza ki **mentése** a szerepkör hozzárendelése befejezéséhez. Megjelenik a listában, hogy a hatókör adott szerepkörhöz rendelt felhasználók az alkalmazást.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Azure szerepköralapú hozzáférés-vezérlés (RBAC) részletes hozzáféréskezelést az Azure biztosít. Szerepalapú segítségével szabályozhatja a felhasználók a munkája elvégzéséhez szükséges hozzáférési szintet. Szerepköralapú hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Azure előfizetés erőforrásokhoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>VSTS-ügynökök készletek

Ügynököket külön-külön kezelése, helyett ügynökök rendezhetők ügynök készletekbe. Egy ügynök-készletet a megosztási határ határozza meg a készlethez tartozó összes ügynököt. A VSTS az ügynök készletek a VSTS-fiókhoz, ami azt jelenti, hogy egy ügynök készlet megoszthatja az csapatprojektek hatókörét. Ügynök készletek kapcsolatos további információkért lásd: [ügynök készletek létrehozása és a várólisták](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adja hozzá a személyes hozzáférési jogkivonat (PAT) Azure verem

Hozzon létre egy személyes hozzáférési jogkivonat VSTS eléréséhez.

1. Jelentkezzen be a VSTS-fiókba, és válassza ki a profil nevét.
2. Válassza ki **kezelése biztonsági** jogkivonatok létrehozásának lapra.

    ![Felhasználói bejelentkezés](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Válassza ki a csapatprojekt](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Adja hozzá a személyes hozzáférési jogkivonat](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Jogkivonat létrehozása](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Másolja a jogkivonatot.

    > [!Note]
    > Mentse a jogkivonat adatai. Ezek az információk nem tárolja, és nem jeleníthető meg újra a weblap hagyja mikor.

    ![Személyes hozzáférési jogkivonat](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Telepítse a VSTS ügynök létrehozása az Azure-veremben központigyorsítótár kiszolgáló létrehozása

1. A Build, az Azure-verem gazdagépen telepített kiszolgálóhoz való csatlakozás.
2. A letöltés és telepítés a build ügynök, mint a személyes szolgáltatás hozzáférési tokent (PAT), és a virtuális gép rendszergazdai fiók futtató.

    ![Build-ügynök letöltése](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Keresse meg azt a mappát a kibontott build ügynök. Futtassa a **run.cmd** egy rendszergazda jogú parancssort a fájlt.

    ![Kibontott build ügynök](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Build ügynök regisztrálása](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. A run.cmd befejezése után további fájlok frissül az összeállítási-ügynök mappájába. A mappát a kibontott tartalma a következő hasonlóan kell kinéznie:

    ![Ügynök frissítés mappa létrehozása](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Az ügynök VSTS mappában tekintheti meg.

## <a name="endpoint-creation-permissions"></a>Végpont létrehozása engedélyek

Végpontok létrehozása, a Visual Studio online-hoz (VSTO) build is üzembe helyezheti Azure Service-alkalmazásokhoz Azure verem. A build ügynök, amely kapcsolódik az Azure-verem VSTS kapcsolatot.

![A VSTO NorthwindCloud mintaalkalmazás](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Jelentkezzen be VSTO, és keresse meg az alkalmazás beállításai oldal.
2. A **beállítások**, jelölje be **biztonsági**.
3. A **VSTS csoportok**, jelölje be **végpont Creators**.

    ![NorthwindCloud végpont Creators](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Az a **tagok** lapon jelölje be **Hozzáadás**.

    ![Tag hozzáadása](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. A **felhasználók és csoportok hozzáadása**, adjon meg egy felhasználónevet, és válassza ki, hogy a felhasználót a felhasználók listáját.
6. Válassza ki **módosítások mentése**.
7. Az a **VSTS csoportok** listáról válassza ki **végpont rendszergazdák**.

    ![NorthwindCloud végpont rendszergazdák](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Az a **tagok** lapon jelölje be **Hozzáadás**.
9. A **felhasználók és csoportok hozzáadása**, adjon meg egy felhasználónevet, és válassza ki, hogy a felhasználót a felhasználók listáját.
10. Válassza ki **módosítások mentése**.

Most, hogy létezik-e a végpont-információkat, a VSTS Azure verem kapcsolatra készen áll a használatra. A build ügynök Azure verem utasításokat lekérése VSTS, és ezután az ügynök információt végpont Azure verem szolgáltatással való kommunikációhoz.

![Ügynök összeállítása](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Az alkalmazás összeállítása fejlesztése

Az oktatóanyag ezen részében lesz:

* Adja hozzá a kódot VSTS-projektbe.
* Önálló webes alkalmazás központi telepítés létrehozásához.
* A folyamatos üzembe helyezési folyamat beállítása

> [!Note]
 > A Windows Server és SQL Server futtatásához hírcsatorna megfelelő képek kell azt az Azure verem környezetet. App Service telepített is kell rendelkeznie. Tekintse meg az App Service dokumentációt "Előfeltételek" szakaszban Azure verem operátor követelményeket.

Hibrid CI/CD alkalmazáskód és infrastruktúra-kódot is alkalmazhatja. Használjon [Azure Resource Manager sablonok, például webes ](https://azure.microsoft.com/resources/templates/) VSTS mindkét felhőben telepítendő alkalmazás kódot.

### <a name="add-code-to-a-vsts-project"></a>Adja hozzá a kódot egy VSTS-projekthez

1. Jelentkezzen be a VSTS egy olyan fiókkal, amely Azure verem projekt létrehozási jogosultsággal rendelkezik. A következő képernyőfelvétel-készítés bemutatja, hogyan csatlakozzon a HybridCICD projekt.

    ![Csatlakozzon a projekthez](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Klónozza a tárházat** hoz létre, és az alapértelmezett webes alkalmazás megnyitása.

    ![Klónozott tárház](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Mindkét felhőben, hozzon létre önálló webes alkalmazások telepítését az App Service szolgáltatások

1. Szerkesztés a **WebApplication.csproj** fájl: válassza ki **Runtimeidentifier** majd `win10-x64.` további információkért lásd: [önálló telepítés](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentációját.

    ![Runtimeidentifier konfigurálása](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Team Explorer segítségével ellenőrizze a kódot a VSTS.

3. Győződjön meg arról, hogy az alkalmazás kódjának be lett-e jelölve, a Visual Studio Team Services.

### <a name="create-the-build-definition"></a>A build definíció létrehozása

1. Jelentkezzen be a VSTS egy olyan fiókkal, amely egy build definition hozhat létre.
2. Keresse meg a **Build webes Applicaiton** a projekthez tartozó lapon.

3. A **argumentumok**, adja hozzá **- r: win10-x64** kódot. Ez szükséges elindítható egy önálló telepítés a .net Core.

    ![Argumentum build hozzáadása](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Futtassa a build. A [önálló telepítés build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat közzéteszi az Azure és az Azure-verem futtatható összetevők.

### <a name="use-an-azure-hosted-build-agent"></a>Használja az Azure üzemeltetett build ügynök

Egy üzemeltetett build-ügynök használatával a VSTS beállítás kényelmes kialakításához, és a webes alkalmazások központi telepítéséhez. Ügynök karbantartása és frissítései a Microsoft Azure, amely lehetővé teszi, hogy egy folyamatos és folyamatos fejlesztési ciklus által automatikusan elvégzett.

### <a name="configure-the-continuous-deployment-cd-process"></a>A folyamatos üzembe helyezés (CD) folyamat beállítása

Visual Studio Team Services (VSTS) és a Team Foundation Server (TFS) adja meg egy magas konfigurálható és kezelhető is legyen folyamat kiadásokhoz fejlesztési, például több környezetekben való átmeneti, minőségi megbízhatósági (QA), és az éles üzem. Ez a folyamat tartalmazhatnak jóváhagyásokat igénylő alkalmazások életciklusának meghatározott szakaszában.

### <a name="create-release-definition"></a>Kiadás definíció létrehozása

Egy kiadási definíciójának létrehozása az utolsó lépést az alkalmazás létrehozása folyamatban. Ez a kiadás definíció kiadási létrehozása és telepítése a build szolgál.

1. Jelentkezzen be VSTS, és navigáljon **összeállítása, és felszabadíthatja a** a projekthez.
2. Az a **kiadásokban** lapon jelölje be  **\[ +]** , majd válassza ki a használni **létrehozás kiadás definition**.

   ![Kiadás definíció létrehozása](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. A **válasszon olyan sablont,**, válassza a **Azure App Service üzembe helyezéséhez**, majd válassza ki **alkalmaz**.

    ![Sablon telepítése](media\azure-stack-solution-hybrid-pipeline\102.png)

4. A **Hozzáadás összetevő**, az a **forrás (Build-definíció)** legördülő menüből válassza ki az Azure-felhő build alkalmazást.

    ![Összetevő hozzáadása](media\azure-stack-solution-hybrid-pipeline\103.png)

5. Az a **csővezeték** lapon jelölje be a **1 fázis**, **1 feladat** csatolása **környezettel kapcsolatos feladatok megtekintése**.

    ![Feldolgozási sor nézet feladatai](media\azure-stack-solution-hybrid-pipeline\104.png)

6. A a **feladatok** lapra, adja meg az Azure-bA a **környezet neve** válassza ki a AzureCloud Traders-Web EP a **Azure-előfizetés** legördülő listából.

    ![Környezeti változók beállítása](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Adja meg a **az Azure app service-nevének**, vagyis a következő képernyőfelvétel-készítés "northwindtraders".

    ![Alkalmazás neve](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Az ügynök fázisban kiválasztása **üzemeltetett VS2017** a a **ügynök várólista** legördülő listából válassza ki.

    ![Üzemeltetett ügynök](media\azure-stack-solution-hybrid-pipeline\107.png)

9. A **telepítése Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezetben.

    ![Válassza ki a csomag vagy mappa](media\azure-stack-solution-hybrid-pipeline\108.png)

10. A **válassza ki a fájl vagy mappa**, jelölje be **OK** való **hely**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Minden módosítások mentése és visszatérés **csővezeték**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\110.png)

12. A a **csővezeték** lapon jelölje be **Hozzáadás összetevő**, és válassza ki a **NorthwindCloud Traders-hajó** a a **forrás (Build-definíció)** legördülő listából.

    ![Adja hozzá az új összetevő](media\azure-stack-solution-hybrid-pipeline\111.png)

13. A **válasszon olyan sablont,**, adja hozzá egy másik környezet. Válasszon **Azure App Service üzembe helyezéséhez** majd **alkalmaz**.

    ![Sablon kiválasztása](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Adja meg az "Azure verem" a **környezetnevet**.

    ![Környezet neve](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Az a **feladatok** lapon található, és válassza ki az Azure-verem.

    ![Azure verem környezetben](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Az a **Azure-előfizetés** legördülő listára, válassza ki a "AzureStack Traders-hajó EP" Azure verem végpont.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Írja be a Azure verem webes alkalmazás neve, ahogyan a **az App service nevének**.

    ![Alkalmazás neve](media\azure-stack-solution-hybrid-pipeline\116.png)

18. A **ügynök kijelölés**, válasszon "AzureStack - bDouglas Fir" címet a **ügynök várólista** legördülő listában.

    ![Válassza ki az ügynök](media\azure-stack-solution-hybrid-pipeline\117.png)

19. A **telepítése Azure App Service**, válassza ki a érvényes **csomag vagy a mappa** a környezetben. A **fájl vagy mappa kijelölése**, jelölje be **OK** mappa **hely**.

    ![Válassza ki a csomag vagy mappa](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Hely jóváhagyása](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Az a **változó** lapján található nevű változó **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. A változó értékét állítsa **igaz**, és a hatókör **Azure verem**.

    ![Változó konfigurálása](media\azure-stack-solution-hybrid-pipeline\120.png)

21. A a **csővezeték** lapon jelölje be a **folyamatos üzembe helyezés eseményindító** ikon a NorthwindCloud Traders-webalkalmazás-összetevő, és a beállított a **folyamatos üzembe helyezés eseményindító** számára **Engedélyezett**.  Hajtsa végre ugyanezt a "NorthwindCloud Traders-hajó" összetevő.

    ![Állítsa a folyamatos üzembe helyezés eseményindító](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Az Azure-verem környezetben, válassza ki a **központi telepítés előtti feltételek** ikon beállítása az eseményindító **kiadása után**.

    ![Központi telepítés előtti feltételek eseményindító beállítása](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Mentse az összes módosítást.

> [!Note]
> Kiadási tevékenységek egyes beállításai esetleg lett automatikusan meghatározott [környezeti változók](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) sablonból kiadás definíciójának létrehozásakor. Ezeket a beállításokat nem lehet módosítani a tevékenység beállításai. Azonban ezek a beállítások a szülő környezet elemek szerkesztheti.

## <a name="create-a-release"></a>Egy kiadási létrehozása

Most, hogy a módosítások a kiadási definíciójához befejeződött, akkor a központi telepítés kezdési idejét. Ehhez az szükséges, a kiadás definícióból kiadási hoz létre. Egy kiadási előfordulhat, hogy hozza létre automatikusan; például a folyamatos üzembe helyezés eseményindító a kiadási definíciója van megadva. Ez azt jelenti, hogy a Forráskód módosítása egy új build indítása és az, hogy új verziót. Azonban ez a szakasz a hoz létre egy új kiadási manuálisan.

1. Az a **csővezeték** lapon nyissa meg a **kiadási** legördülő listában, és válassza a **létrehozása**.

    ![Egy kiadási létrehozása](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Adjon meg egy leírást, a kiadás, ellenőrizze, hogy, hogy helyes-e az összetevők vannak-e jelölve, és válassza **létrehozása**. Néhány másodpercen belül egy szalagcím jelenik meg, amely azt jelzi, hogy az új kiadási lett létrehozva, és a kiadás neve hivatkozásként jelenik meg. Válassza ki a hivatkozásra kattintva megtekintheti a kiadási összefoglalás lapon.

    ![Kiadás létrehozási szalagcím](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Az összefoglalás lapon kiadás a kiadási részleteit jeleníti meg. "Kiadás-2", az alábbi képernyőfelvételen a **környezetek** szakasz azt mutatja be a **központi telepítési állapot** az az Azure-bA "Folyamatban", és az Azure-verem állapota "sikeres". Ha az Azure környezetbe a központi telepítési állapotának vált, "SUCCEEDED", a fejléc jelenik meg arról, hogy a kiadás készen a jóváhagyásra. Ha a központi telepítés függőben vagy sikertelen volt, a kék **(i)** információs ikon jelenik meg. Egy előugró ablak, amely tartalmazza a késleltetés vagy a hiba okának megjelenítéséhez megjelenő ikonra mutat.

    ![Kiadás összegző lapja](media\azure-stack-solution-hybrid-pipeline\202.png)

Más nézetekhez, például a listát a kiadott, is megjelenít egy ikont, amely jelzi a jóváhagyása függőben. Erre az ikonra az előugró környezet nevét és a telepítésével kapcsolatos további részleteket jeleníti meg. Akkor is könnyen, a rendszergazda című összesített végrehajtási állapotát a kiadások, és ellenőrizze, amely kiadásokban jóváhagyásra várnak.

### <a name="monitor-and-track-deployments"></a>A figyelő és nyomon követése központi telepítések

Ez a szakasz bemutatja, hogyan figyelheti, és nyomon követheti a központi telepítések. A két Azure App Services-webhelyek üzembe helyezéséhez a kiadási biztosítja jó példa.

1. A "Release-2" összefoglaló oldalon válassza **naplók**. A telepítés során ezen a lapon látható az élő napló az ügynöktől. A bal oldali panelen a központi telepítés egyes környezetekben minden egyes művelet állapotát jeleníti meg.

    Választhat, hogy egy személy ikonra a **művelet** oszlop egy központi telepítés előtti vagy utáni jóváhagyást, aki jóvá (vagy nem utasítható el) a telepítésben, és az üzenetet kapott.

2. A telepítés befejezése után a teljes naplófájlban a jobb oldali ablaktáblán jelennek meg. Kiválaszthatja a **lépés** a bal oldali ablaktáblán, hogy a naplófájl az egyetlen lépés, például a "Feladat inicializálása". Megtekintheti az egyes naplók megkönnyíti a nyomon követése és hibakeresése telepítésének részét. Is **mentése** egy lépést, a naplófájl vagy **összes naplók letöltéséhez, zip**.

    ![Kiadás naplók](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Nyissa meg a **összegzés** lapon, a kiadás kapcsolatos általános információkat láthatja. Ez a nézet a build, a telepítették a, központi telepítési állapot és az egyéb információk az kiadásról részleteit jeleníti meg.

4. Válassza ki egy környezeti hivatkozás (**Azure** vagy **Azure verem**) információ meglévő és az olyan központi telepítés az adott környezetben való megjelenítéséhez. Ezek a nézetek gyorsan győződjön meg arról, hogy az azonos buildjét mindkét környezetben telepített is használhatja.

5. Nyissa meg a **éles telepített** a böngészőben. Például az Azure App Services webhely, nyissa meg az URL-cím `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>További lépések

* Azure Cloud mintájával kapcsolatos további tudnivalókért lásd: [felhőalapú kialakítási mintájával](https://docs.microsoft.com/azure/architecture/patterns).
