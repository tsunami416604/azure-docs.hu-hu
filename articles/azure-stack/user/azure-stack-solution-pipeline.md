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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 49a80805c976e5584bb158965583a03eda68cc46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Oktatóanyag: Azure és az Azure alkalmazások telepítése verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Egy hibrid folyamatos integráció/folyamatos kézbesítési (CI/CD) folyamat létre, tesztelheti, és az alkalmazás üzembe helyezése több felhők teszi lehetővé.  Ebben az oktatóanyagban egy minta környezet fog létrehozni:
 
> [!div class="checklist"]
> * Indítsa el a kód véglegesíti a Visual Studio Team Services (VSTS) tárházhoz alapján új buildverziót.
> * A felhasználói tesztelés globális Azure automatikusan telepíteni a újonnan beépített kódot.
> * Miután a kód ment át tesztelésen, automatikusan telepítheti őket Azure verem.

### <a name="about-the-hybrid-delivery-build-pipe"></a>Tudnivalók a hibrid kézbesítési cső létrehozása

Alkalmazás központi telepítési folytonosságát, biztonságot és megbízhatóságot az alapvető fontosságú a szervezet és a fejlesztői csapat kritikus fontosságú. A hibrid CI/CD sorban a folyamatok képes egyesíteni a helyszíni környezetben és a nyilvános felhő között. Az alkalmazás közötti váltás nélkül helyét módosíthatja.

Ez a megközelítés lehetővé teszi a fejlesztői eszközök konzisztens karbantartása. Az Azure nyilvános felhőjében és a helyszíni Azure verem környezet konzisztens eszközök azt jelenti, hogy sokkal könnyebben CI/CD fejlesztői gyakorlatot. Az alkalmazások és szolgáltatások Azure vagy az Azure-verem telepítve felcserélhető, és ugyanazt a kódot futtathatja az egyik helyen, lehetővé teszi a helyszíni és nyilvános felhők szolgáltatásait és képességeit.

További információ:
 - [Mi az a folyamatos integrációt?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [Mi az a folyamatos kézbesítési?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz rendelkezik néhány összetevőt egy hibrid CI/CD folyamat létrehozásához. Készítse elő a bizonyos ideig tarthat.
 
 - Egy Azure OEM-hardver partner előfordulhat, hogy telepíteni egy éles Azure-vermet, és minden felhasználó is telepítheti egy Azure verem Development Kit (ASDK). 
 - Azure verem operátort kell is telepítheti az App Service, csomagok és ajánlatok létrehozása, bérlő előfizetés létrehozása és a Windows Server 2016-lemezkép hozzáadása.

Ha már rendelkezik néhány ezeket az összetevőket, győződjön meg arról, hogy azok megfelelnek megkezdése előtt.

Ez a témakör feltételezi, hogy rendelkezik-e bizonyos Azure és az Azure-verem ismerete. Ha azt szeretné, további, a folytatás előtt mindenképpen indítsa el az alábbi témakörök:


Ez az oktatóanyag feltételezi, hogy rendelkezik-e bizonyos Azure és az Azure-verem ismerete. 

Ha azt szeretné, további, a folytatás előtt, Kezdésként használhatja az alábbi témakörök:
 - [Bevezetés az Azure-bA](https://azure.microsoft.com/overview/what-is-azure/)
 - [Az Azure verem kulcsfogalmak](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

 - Hozzon létre egy [webalkalmazás](https://docs.microsoft.com/azure/app-service/app-service-web-overview) az Azure-ban. Jegyezze meg az új webes alkalmazás URL-CÍMÉT, mert a rendszer később.

Azure Stack
 - Az Azure-verem integrált rendszert használja, vagy telepítse a Azure verem Development Kit (ASDK) alábbi hivatkozásokon elérhető:
    - A ASDK telepítésével kapcsolatos részletes utasításokat található "[oktatóanyag: telepítéséhez a telepítő használatával ASDK](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - Automatizálható a ASDK a telepítés utáni lépéseket a következő PowerShell-parancsfájl számos [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!note]  
    > A ASDK telepítés befejeződik, ezért ennek megfelelően tervezheti a hét órát vesz igénybe.

 - Telepítése [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure verem PaaS-szolgáltatások. 
 - Hozzon létre [terv/ajánlatok](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) az Azure-verem környezeten belül. 
 - Hozzon létre [bérlői előfizetéshez](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) az Azure-verem környezeten belül. 
 - Webalkalmazások létrehozása a bérlői előfizetésen belül. Jegyezze fel az új webes alkalmazás URL-CÍMÉT a későbbiekben használhassa.
 - Telepítse a VSTS virtuális gépet, továbbra is a bérlő előfizetésen belül.
 - Windows Server 2016 tulajdonsággal rendelkező virtuális gépet a .NET 3.5 szükséges. Ez a virtuális gép az Azure, a titkos build megbízottként épül. 

### <a name="developer-tools"></a>Fejlesztői eszközök

 - Hozzon létre egy [VSTS munkaterület](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). A regisztrációs folyamat létrehoz egy nevű projekt **MyFirstProject**.
 - [Telepítse a Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) és [bejelentkezés az VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - A projekt csatlakozni és [helyileg klónozni](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!note]  
 > Szüksége lesz Azure verem megfelelő lemezképekkel hírcsatorna-(Windows Server és SQL) futtatásához, és a telepített App Service-szolgáltatást.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Az a Visual Studio Team Services integráció előkészítését a titkos build és a kiadási ügynök

### <a name="prerequisites"></a>Előfeltételek

A Visual Studio Team Services (VSTS) szemben Azure Resource Manager használata egyszerű szolgáltatás hitelesíti. Egy Azure verem előfizetésben szereplő erőforrások kiépítése tennie VSTS szükséges közreműködői állapotát.

Az alábbiakban a magas szintű lépéseket, úgy kell konfigurálni, ilyen hitelesítés engedélyezése:

1. Szolgáltatás egyszerű kell létrehozni, vagy egy meglévő használható.
2. Hitelesítési kulcsokat kell létrehozni a szolgáltatásnevet.
3. Azure-előfizetés verem kell szerepköralapú hozzáférés-vezérlés engedélyezi az egyszerű Szolgáltatásnevet a közreműködői szerepkör részét keresztül ellenőrizhető.
4. A VSTS új szolgáltatásdefiníció segítségével kell létrehozni. a végpontok Azure verem, valamint a SPN információkat.

### <a name="service-principal-creation"></a>Egyszerű szolgáltatás létrehozása

Tekintse meg a [egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) hozzon létre egy egyszerű szolgáltatást, és válassza a Web App/API az alkalmazás típusra vonatkozó utasításokat.

### <a name="access-key-creation"></a>Hozzáférési kulcs létrehozása

Egy egyszerű hitelesítési kulcs használatát igényli, kövesse az ebben a szakaszban a kulcs létrehozásához.


1. Válassza ki az alkalmazást az Azure Active Directory **Alkalmazásregisztrációk** területén.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Jegyezze fel a értékének **Alkalmazásazonosító**. Ezt az értéket fogja használni, amikor a szolgáltatási végpont konfigurálása a VSTS.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. A hitelesítési kulcs létrehozásához válassza a **Beállítások** elemet.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.
 
    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Adjon meg egy leírást és egy időtartamot a kulcshoz. Ha elkészült, kattintson a **Mentés** elemre.
 
    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    A kulcs mentése után megjelenik a kulcs értéke. Másolja ezt az értéket, mivel később nem lesz lehetősége lekérni a kulcsot. Megadja a **kulcs értékét** az alkalmazás azonosítójával jelentkezzen be az alkalmazás számára. A kulcsértéket olyan helyen tárolja, ahonnan az alkalmazás le tudja kérni.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>A bérlőazonosító beszerzése

A végpont-konfiguráció részeként VSTS igényel a **Bérlőazonosító** , amely megfelel az AAD-címtárában, az Azure-verem stamp állított be. Kövesse az ebben a szakaszban található gyűjtse össze a bérlő azonosítója.

1. Válassza az **Azure Active Directory** elemet.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. A bérlőazonosító lekéréséhez válassza ki az Azure AD-bérlőjéhez tartozó **Tulajdonságok** elemet.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Másolja ki a **Címtár-azonosítót**. Ez az érték a bérlőazonosítója.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>A szolgáltatás egyszerű engedélyeket az Azure-verem előfizetés erőforrások telepítése 

Az előfizetés az erőforrások eléréséhez az alkalmazást egy szerepkörhöz kell rendelni. Döntse el, melyik szerepkört jelöli a megfelelő engedélyekkel az alkalmazáshoz. A rendelkezésre álló szerepkörökkel kapcsolatos további tudnivalókért lásd: [RBAC: beépített szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

A hatókör szintjén található az előfizetés, erőforráscsoportból vagy erőforrás állíthatja be. Engedélyek hatókör alacsonyabb szintre származnak. Például egy alkalmazást az olvasó szerepkört erőforráscsoport hozzáadása azt jelenti, hogy ez az erőforráscsoport és a benne található erőforrásokat tudja olvasni.

1. Nyissa meg a szint szeretne hozzárendelni az alkalmazást. Például egy szerepkört az előfizetés hatókörből rendeléséhez jelölje **előfizetések**. Ehelyett kiválaszthatja egy erőforráscsoport vagy az erőforrás.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Válassza ki a **előfizetés** (erőforráscsoport vagy erőforrás) az alkalmazást.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Válassza ki **hozzáférés-vezérlés (IAM)**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Válassza a **Hozzáadás** lehetőséget.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Jelölje ki az alkalmazáshoz hozzárendelni kívánt szerepkört. Az alábbi képen látható a **tulajdonos** szerepkör.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Alapértelmezés szerint Azure Active Directory-alkalmazások nem jelennek meg a rendelkezésre álló beállításokat. Az alkalmazás megkereséséhez kell **a nevét adja meg** a keresési mezőbe. Válassza ki azt.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Válassza ki **mentése** a szerepkör hozzárendelése befejezéséhez. Megjelenik a listában, hogy a hatókör adott szerepkörhöz rendelt felhasználók az alkalmazást.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Azure szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi, hogy a részletes hozzáféréskezelést az Azure-bA. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges. Szerepköralapú hozzáférés-vezérléssel kapcsolatos további információkért lásd: [Azure előfizetés erőforrásokhoz való hozzáférés kezelése](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>VSTS-ügynökök készletek

Ügynököket külön-külön kezelése, helyett ügynökök ügynök készletekbe rendezheti. Egy ügynök-készletet a megosztási határ határozza meg a készlethez tartozó összes ügynököt. A VSTS ügynök készletek hatóköre a VSTS-fiókot. ezért egy ügynök készlet lehet megosztani csapatprojektek. További információt és VSTS-ügynökök címkészletek létrehozásával kapcsolatos oktatóanyag: [ügynök készletek létrehozása és a várólisták](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Adja hozzá a személyes hozzáférési jogkivonat (PAT) Azure verem

1. Jelentkezzen be a VSTS-fiókba, és válassza ki a profil nevét.
2. Válassza ki **kezelése biztonsági** jogkivonatok létrehozásának lapra.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Másolja a jogkivonatot.
    
    > [!note]  
    > A token információkhoz. Azt nem jelenik meg újra ez a képernyő megszűnését követően. 
    
    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Telepítse a VSTS ügynök létrehozása az Azure-veremben központigyorsítótár kiszolgáló létrehozása

1.  A Build, az Azure-verem gazdagépen telepített kiszolgálóhoz való csatlakozás.

2.  A letöltés és telepítés a build ügynök, mint a személyes szolgáltatás hozzáférési tokent (PAT), és a virtuális gép rendszergazdai fiók futtató.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Lépjen a kibontott build-ügynök mappájába. Futtassa a **run.cmd** egy rendszergazda jogú parancssort a fájlt. 

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  A run.cmd befejezése után a mappát a kibontott tartalma a következő hasonlóan kell kinéznie:

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Most már megtekintheti az ügynök VSTS mappában.

## <a name="endpoint-creation-permissions"></a>Végpont létrehozása engedélyek

Felhasználók hozhatnak létre végpontokat, így VSTO buildek telepíthet Azure szolgáltatás alkalmazásokat a verem. A build ügynök, amely ezután csatlakozik az Azure-verem VSTS kapcsolatot. 

![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Az a **beállítások** menü **biztonsági**.
2. Az a **VSTS csoportok** bal oldalán válassza ki a lista **végpont Creators**. 

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. Az a **tagok** lapon jelölje be **+ Hozzáadás**. 

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a listából.
5. Kattintson a **módosítások mentése**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. Az a **VSTS csoportok** bal oldalán válassza ki a lista **végpont rendszergazdák**.
7. Az a **tagok** lapon jelölje be **+ Hozzáadás**.
8. Írjon be egy felhasználónevet, és válassza ki, hogy a felhasználó a listából.
9. Kattintson a **módosítások mentése**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    A build ügynök Azure verem átveszi az utasításokat a VSTS, amely az Azure-verem szolgáltatással való kommunikációhoz majd végpont információt. 
    
    Azure verem kapcsolatra VSTS készen áll.

## <a name="develop-your-application"></a>Az alkalmazás fejlesztése

Hibrid CI/CD webalkalmazás telepítése az Azure és az Azure-vermet, és küldje el a módosításokat mindkét felhőben való automatikus beállítása.

> [!note]  
> Szüksége lesz Azure verem megfelelő lemezképekkel hírcsatorna-(Windows Server és SQL) futtatásához, és a telepített App Service-szolgáltatást. Tekintse meg az App Service dokumentációt "Előfeltételek" szakaszban Azure verem operátor követelményeket.

### <a name="add-code-to-vsts-project"></a>Adja hozzá a kódot VSTS-projekthez

1. Jelentkezzen be a Visual Studio egy olyan fiókkal, amely Azure verem projekt létrehozási jogosultsággal rendelkezik.

    Hibrid CI/CD alkalmazáskód és infrastruktúra-kódot is alkalmazhatja. Használjon [Azure Resource Manager sablonok, például webes ](https://azure.microsoft.com/resources/templates/) alkalmazáskódot a VSTS mindkét felhőben.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Klónozza a tárházat** hoz létre, és az alapértelmezett webes alkalmazás megnyitása.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Mindkét felhőben, hozzon létre önálló webes alkalmazások telepítését az App Service szolgáltatások

1. Szerkessze a **WebApplication.csproj** fájl: válassza ki **Runtimeidentifier** , és adja hozzá `win10-x64.` további információkért lásd: [önálló telepítés](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentáció .

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Ellenőrizze a kódot a csapat Explorerrel VSTS.

3. Győződjön meg arról, hogy az alkalmazás kódjának ellenőrizte a Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>A build definíció létrehozása

1. Jelentkezzen be a VSTS build definíciók létrehozására megerősítéséhez.

2. Adja hozzá **- r: win10-x64** kódot. Erre akkor szükség, elindítható egy önálló telepítés a .net Core. 

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Futtassa a build. A [önálló telepítés build](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) folyamat közzéteszi az Azure és az Azure-verem futtatható összetevők.

### <a name="using-an-azure-hosted-agent"></a>Az Azure üzemeltetett ügynök használatával

Egy központigyorsítótár-ügynök használatával a VSTS beállítás kényelmes létre és telepíthet webalkalmazásokat. Microsoft Azure-ban folyamatosan, folyamatos fejlesztési, tesztelési és telepítésének engedélyezése automatikusan végzi a karbantartása és frissítései.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Kezelése és konfigurálása a folyamatos Deployment (CD) folyamat

Visual Studio Team Services (VSTS) és a Team Foundation Server (TFS) adja meg egy magas konfigurálható és kezelhető is legyen folyamat kiadásokban fejlesztési, például több környezetekben való átmeneti, QA és éles környezetek; beleértve a jóváhagyását igénylő adott szakaszában.

### <a name="create-release-definition"></a>Kiadás definíció létrehozása

![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Válassza ki a  **\[ +]** hozzáadása az új verziót a **kiadásokban lapon** VSO Build és kiadás lapján.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Alkalmazza a **Azure App Service üzembe helyezéséhez** sablont.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\103.png)

3. A Hozzáadás összetevő legördülő menüből **összetevő hozzáadása** az Azure-felhő build alkalmazás.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\104.png)

4. Az adatcsatorna lapon válassza ki a **fázis**, **feladat** a környezet hivatkozásra, és állítsa be az Azure felhőalapú környezet értékeket.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Állítsa be a **környezetnevet** válassza ki az Azure **előfizetés** Azure felhőbe végpont.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\106.png)

6. A környezet neve, állítsa be a szükséges **az Azure app service-nevének**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Adja meg **üzemeltetett VS2017** ügynök várólista Azure felhőben üzemeltetett környezetben alatt.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\108.png)

8. Azure App Service telepítése menüben válasszon ki érvényes **csomag vagy a mappa** a környezetben. Válassza ki **OK** való **mappába**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Minden módosítások mentése és visszatérés **kiadás adatcsatorna**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Adja hozzá a **új összetevő** a összeállítása az Azure-verem alkalmazás kiválasztása.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Vegye fel egy további környezet alkalmazása a **Azure App Service üzembe helyezéséhez**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Az új környezet neve **Azure verem**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Az Azure-verem környezet alatt található **feladat** fülre.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Válassza ki a **előfizetés** Azure verem végpont.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Állítsa be a Azure verem webes alkalmazás neve, ahogyan a **az App service nevének**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Válassza ki a **Azure verem ügynök**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\118.png)

17. A központi telepítése Azure App Service alatt csoportban jelölje be az érvényes **csomag vagy a mappa** a környezetben. Kattintson az OK gombra **mappába**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\120.png)

18. Változó lapon nevű változó hozzáadása **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, mint az értékét állítsa **igaz**, és a hatókör **Azure verem**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Válassza ki a **folyamatos** telepítési indíthat mindkét összetevők ikonra, és a központi telepítés folytatja eseményindító engedélyezése.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Válassza ki a **központi telepítés előtti** feltételek ikon az azure-ban a verem környezetben, és állítsa az eseményindító **kiadása után**.

21. Minden módosítások mentéséhez.

> [!note]  
> A feladatok egyes beállításai esetleg lett automatikusan meghatározott [környezeti változók](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) sablonból kiadás definíciójának létrehozásakor. Ezeket a beállításokat nem lehet módosítani a feladatbeállítások; Ehelyett ki kell választania a szülő környezet cikk ezek a beállítások szerkesztéséhez.

## <a name="create-a-release"></a>Egy kiadási létrehozása

Most, hogy a módosítások a kiadási definíciójához befejeződött, akkor a központi telepítés kezdési idejét. Ehhez az szükséges, a kiadás definícióból kiadási hoz létre. Egy kiadási előfordulhat, hogy hozza létre automatikusan; például a folyamatos üzembe helyezés eseményindító a kiadási definíciója van megadva. Ez azt jelenti, hogy a Forráskód módosítása egy új build indítása és az, hogy új verziót. Azonban ez a szakasz a hoz létre egy új kiadási manuálisan.

1. Nyissa meg a **kiadási** legördülő listában, és válassza a **létrehozása**.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Adjon meg egy leírást, a kiadás, ellenőrizze, hogy helyes-e az összetevők van kiválasztva, majd válassza a **létrehozása**. Néhány másodpercen belül egy szalagcím figyelmezteti arra, hogy létrejött-e az új verziót. Válassza ki a kapcsolat (a kiadási nevét).

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. A kiadási összefoglalás lapon megnyitja a kiadás a részletekkel. Az a **környezetek** szakaszban módosítsa "Folyamatban" a "QA" környezet központi telepítési állapotának látni fogja a "SUCCEEDED", és ezen a ponton egy szalagcím akkor jelenik meg, amely azt jelzi, hogy a kiadás most jóváhagyásra vár. Amikor egy olyan környezetben való telepítést függőben vagy sikertelen volt, a kék (i) információs ikon jelenik meg. Ennek oka tartalmazó előugró ablak megtekintéséhez mutasson.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\202.png)

Más nézetekhez, például a listát, kiadásainak is, amely jelzi a jóváhagyási ikon megjelenítése függőben. Az ikon jelzi egy előugró ablak, a környezet neve és a további részleteket tartalmazó mutat rá. Ez megkönnyíti, hogy a rendszergazda mely kiadásokban váró jóváhagyási, valamint a teljes folyamat összes kiadásainak megjelenítéséhez.

### <a name="monitor-and-track-deployments"></a>A figyelő és nyomon követése központi telepítések

Ebben a szakaszban láthatja, hogyan figyelheti és nyomon követheti a központi telepítések – ebben a példában két Azure App Services-webhelyekre – az előző szakaszban létrehozott kiadásából.

1. Kiadás összefoglalás lapon válassza ki a **naplók** hivatkozásra. Közben a központi telepítést, az ezen a lapon látható az élő napló, az ügynök és a bal oldali ablaktáblán, az arra utal, hogy minden műveletet a telepítési folyamat minden környezet állapotáról.

    Válassza ki a ikonra a **művelet** részleteinek ki a központi telepítés előtti vagy utáni jóváhagyásra oszlop jóváhagyott (vagy nem utasítható el) a telepítésben, és az üzenetet a felhasználó által megadott.

2. A telepítés befejezése után, akkor a teljes naplófájl a jobb oldali ablaktáblán jelennek meg. Válassza ki valamelyik a **lépéseket feldolgozni** a bal oldali ablaktáblán, hogy a napló csak az adott lépés tartalmának megjelenítése. Ez megkönnyíti a nyomkövetést, és egyes részeire telepítésének hibakeresését. Azt is megteheti töltse le, az egyes naplófájlok, vagy egy zip minden a naplófájlok az ikonok és a lapon lévő hivatkozások.

    ![Helyettesítő szöveg](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Nyissa meg a **összegzés** fülre a kiadási általános részleteinek megtekintéséhez. Azt mutatja, hogy a részleteit, valamint a build – a központi telepítési állapotát és egyéb információkat a kiadás együtt lett telepítve a környezetben.

4. Jelölje ki a **környezet hivatkozások** vonatkozó, létező, és olyan központi telepítés az adott környezet további részletek megtekintéséhez. E lapok használatával győződjön meg arról, hogy az azonos buildjét telepített mindkét környezetben.

5. Nyissa meg a **éles telepített** a Tallózás. Például az Azure App Service szolgáltatások webhely, az URL-címről `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>További lépések

- Azure Cloud mintájával kapcsolatos további tudnivalókért lásd: [felhőalapú kialakítási mintájával](https://docs.microsoft.com/azure/architecture/patterns).
