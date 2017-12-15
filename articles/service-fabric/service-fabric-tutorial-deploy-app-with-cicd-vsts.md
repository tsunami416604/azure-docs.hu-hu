---
title: "Az Azure Service Fabric folyamatos integrációt (Team Services) alkalmazás központi telepítését |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthat be folyamatos integrációt és a Visual Studio Team Services használatával a Service Fabric-alkalmazás központi telepítését.  Alkalmazás üzembe helyezése az Azure Service Fabric-fürt."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 2fb7ab906208a58c0b5cd3af8b53188fbab94029
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2017
---
# <a name="deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>A Service Fabric-fürt CI/CD az alkalmazás központi telepítését
Ez az oktatóanyag három sorozatából és azt ismerteti, hogyan állíthat be folyamatos integrációt és a Visual Studio Team Services használata az Azure Service Fabric-alkalmazások központi telepítését.  Egy meglévő Service Fabric-alkalmazás van szükség, az alkalmazás létre [létre olyan .NET alkalmazás](service-fabric-tutorial-create-dotnet-app.md) példaként szolgál.

A sorozat három része a megismerheti, hogyan:

> [!div class="checklist"]
> * A verziókövetési rendszerrel hozzáadása a projekthez
> * Hozzon létre egy build definition Team Services
> * Hozzon létre egy kiadási definition Team Services
> * Automatikus központi telepítése és alkalmazás frissítése

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * [A .NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md)
> * [Telepítse központilag az alkalmazást egy távoli fürthöz](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * Konfigurálja a CI/CD Visual Studio Team Services használatával
> * [Figyelés és diagnosztika az alkalmazás beállítása](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Telepítse a Visual Studio 2017](https://www.visualstudio.com/) és telepítse a **Azure fejlesztési** és **ASP.NET és a webes fejlesztési** munkaterhelések.
- [A Service Fabric SDK telepítése](service-fabric-get-started.md)
- Létrehozhat például egy Azure, a Windows Service Fabric-fürt által [oktatóanyag](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- Hozzon létre egy [Team Services-fiók](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).

## <a name="download-the-voting-sample-application"></a>Töltse le a szavazási mintaalkalmazást
Ha Ön nem összeállítása a szavazási mintaalkalmazást [rész az oktatóanyag adatsorozat](service-fabric-tutorial-create-dotnet-app.md), tölthető le. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Készítse elő a közzétételi profilt
Most, hogy megismerte [az alkalmazások](service-fabric-tutorial-create-dotnet-app.md) és [telepítve az alkalmazás az Azure-bA](service-fabric-tutorial-deploy-app-to-party-cluster.md), készen áll a beállíthat folyamatos integrációt.  Először készítsen egy közzétételi profil számára az alkalmazáson belül a telepítési folyamat, amely végrehajtja a Team Services belül.  A közzétételi profil, amelyekre a fürtöt, hogy a korábban létrehozott kell konfigurálni.  Indítsa el a Visual Studiót, és nyissa meg a meglévő Service Fabric-alkalmazás projekt.  A **Megoldáskezelőben**, kattintson a jobb gombbal az alkalmazást, és válassza ki **közzététel...** .

Válassza ki a cél-profil belül a projektek használja a folyamatos integrációt munkafolyamat, például a felhő.  Adja meg a fürt kapcsolati végpontot.  Ellenőrizze a **az alkalmazás frissítése** jelölőnégyzetet, hogy az alkalmazás minden központi telepítést, Team Services frissíti.  Kattintson a **mentése** hivatkozás mentse a beállításokat a közzétételi profilt, majd **Mégse** a párbeszédpanel bezárásához.  

![Leküldéses profil][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>A Visual Studio megoldás, hogy egy új Team Services Git-tárház megosztása
Megoszthatja az alkalmazás forrásfájljait a Team Services team projektbe, így a buildek is létrehozhat.  

Hozzon létre egy új helyi Git-tárház a projekthez kiválasztásával **adja hozzá a verziókövetési** -> **Git** az állapotsoron a Visual Studio jobb alsó sarkában. 

Az a **leküldéses** megtekintése **Team Explorer**, jelölje be a **Git-tárház közzététele** gombra kattint, a **leküldése a Visual Studio Team Services**.

![Leküldéses Git-tárház][push-git-repo]

Ellenőrizze az e-maileket, és válassza ki azt a fiókot a a **Team Services tartomány** legördülő listán. Adja meg a tárház nevét, és válassza ki **közzététel tárház**.

![Leküldéses Git-tárház][publish-code]

A tárház közzétételi hoz létre új neve megegyezik a helyi tárház fiókját. A tárház létrehozásához egy meglévő csapatprojektben, kattintson a **speciális** melletti **tárház** nevet, és válassza ki a csapatprojekt. Megtekintheti a kódot a weben kiválasztásával **látja, a weben**.

## <a name="configure-continuous-delivery-with-vsts"></a>Folyamatos kézbesítési VSTS konfigurálása
Team Services build definícióját egy munkafolyamatot, amelyik build ismertetett lépések egymás után végrehajtott áll ismerteti. A build definíció létrehozása, amely, amely létrehozza a Service Fabric-alkalmazáscsomagot, és más összetevők, a Service Fabric-fürt központi telepítése. További információ [Team Services build definíciók](https://www.visualstudio.com/docs/build/define/create). 

Team Services kiadás definícióját egy munkafolyamatot, amely egy alkalmazáscsomagot telepít egy fürthöz ismerteti. Együttes használatuk esetén a build meghatározására és kiadás definition hajtható végre a forrásfájlok egy a fürtben futó alkalmazás végződő kezdve a teljes munkafolyamat. További tudnivalók Team Services [definíciók kiadási](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-definition"></a>A build definíció létrehozása
Nyisson meg egy webböngészőt, és keresse meg a következő új csapatprojektbe: [https://&lt;myaccount&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting). 

Válassza ki a **Build & kiadási** lapra, majd **buildek**, majd **+ új definition**.  A **válasszon olyan sablont,**, jelölje be a **Azure Service Fabric-alkalmazás** sablont, és kattintson **alkalmaz**. 

![Build sablon kiválasztása][select-build-template] 

A **feladatok**, írja be a "Kihelyezett VS2017" a **ügynök várólista**. 

![Válassza ki a feladatok][save-and-queue]

A **eseményindítók**, úgy, hogy engedélyezze a folyamatos integrációt **állapota indítás**.  Válassza ki **mentéséhez és a feldolgozási sor** build manuális elindítása.  

![Válassza ki az eseményindítók][save-and-queue2]

Épít is eseményindító leküldéses vagy be. A létrehozási folyamat állapotának ellenőrzéséhez térjen át a **buildek** fülre.  Miután ellenőrizte, hogy a létrehozás sikeresen végrehajtja-e, adja meg egy kiadási-definíciót, amely telepíti az alkalmazást egy fürt. 

### <a name="create-a-release-definition"></a>Egy kiadási definíció létrehozása  

Válassza ki a **Build & kiadási** lapra, majd **kiadásokban**, majd **+ új definition**.  A **válasszon olyan sablont,**, jelölje be a **Azure Service Fabric telepítési** sablont a listából, majd **alkalmaz**.  

![Kiadási sablon kiválasztása][select-release-template]

Válassza ki **feladatok**->**környezet 1** , majd **+ új** hozzáadni egy új fürt-kapcsolatot.

![Fürt-kapcsolat hozzáadása][add-cluster-connection]

Az a **új Service Fabric-kapcsolat hozzáadása** megtekintéséhez válassza ki **tanúsítványalapú** vagy **Azure Active Directory** hitelesítés.  Adja meg a kapcsolat neve "mysftestcluster" és "tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000" a fürt végpontja (vagy a fürt esetében helyez üzembe a végpont). 

A tanúsítványalapú hitelesítéshez, adja hozzá a **kiszolgálói tanúsítvány-ujjlenyomat** a a fürt létrehozásához használt tanúsítvány.  A **ügyféltanúsítvány**, vegye fel az ügyfél tanúsítványfájlját base 64 kódolás. A Súgó előugró ablak jelenik meg, hogy a mező alakjának adott base-64 kódolású tanúsítvány kapcsolatos információk. Is hozzáadhat a **jelszó** a tanúsítvány.  A fürt vagy a kiszolgáló tanúsítványát is használhatja, ha nincs külön ügyfél-tanúsítványt. 

Az Azure Active Directory hitelesítő adatokkal, adja hozzá a **kiszolgálói tanúsítvány-ujjlenyomat** kapcsolódik a fürthöz használni kívánt a fürt és a hitelesítő adatok létrehozásához használt kiszolgálói tanúsítvány a **felhasználónév** és **jelszó** mezőket. 

Kattintson a **Hozzáadás** a fürt kapcsolat mentéséhez.

Ezután adja hozzá a build összetevő az adatcsatornához, a kiadás definition megtalálhatja a build kimenete. Válassza ki **csővezeték** és **összetevők**->**+ Hozzáadás**.  A **forrás (Build-definíció)**, válassza ki a korábban létrehozott build definíciót.  Kattintson a **Hozzáadás** build összetevő mentéséhez.

![Összetevő felvétele][add-artifact]

Engedélyezze a folyamatos üzembe helyezés eseményindító, hogy a kiadás automatikusan létrejön a build befejeződésekor. Kattintson a lightning összetevő, engedélyezze az eseményindító, és kattintson a **mentése** menteni a kiadási definícióját.

![Eseményindító engedélyezése][enable-trigger]

Válassza ki **+ kiadási** -> **létrehozása kiadási** -> **létrehozása** kiadási manuális létrehozásához.  Győződjön meg arról, hogy a telepítés sikeres volt, és az alkalmazás fut a fürtön.  Nyisson meg egy webböngészőt, és keresse meg [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Vegye figyelembe a verziója, ebben a példában "1.0.0.20170616.3". 

## <a name="commit-and-push-changes-trigger-a-release"></a>Véglegesítse és módosításokat, kiadási indítás
Ellenőrizheti, hogy működik-e a folyamatos integrációs folyamat néhány kódot megváltozik Team Services ellenőrzésével.    

Amikor tartalmat ír a kódot, a automatikusan kötetblokkok változásait a Visual Studio. A függőben lévő módosítások ikonra (kiválasztásával a helyi Git-tárház módosításainak véglegesítése![Függőben][pending]) a lévő állapotjelző sáv jobb alsó.

Az a **módosítások** Team Intézőben, vegye fel a frissítést leíró üzenet és a változtatások véglegesítése a határidő.

![Az összes véglegesítése][changes]

Válassza ki a közzé nem tett változás állapotikon sávjának (![közzé nem tett módosítások][unpublished-changes]) vagy a csapat Explorer szinkronizálási nézetet. Válassza ki **leküldéses** frissítse a kódot a Team Services/TFS számára.

![Küldje el a módosításokat][push]

Build kérdez le a módosításokat, hogy Team Services automatikusan váltja ki.  Ha a build definition sikeresen befejeződött, a kiadási automatikusan létrejön, és elindítja a frissíteni az alkalmazást a fürtön.

A létrehozási folyamat állapotának ellenőrzéséhez térjen át a **buildek** lapján **Team Explorer** a Visual Studio.  Miután ellenőrizte, hogy a létrehozás sikeresen végrehajtja-e, adja meg egy kiadási-definíciót, amely telepíti az alkalmazást egy fürt.

Győződjön meg arról, hogy a telepítés sikeres volt, és az alkalmazás fut a fürtön.  Nyisson meg egy webböngészőt, és keresse meg [http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/](http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/).  Vegye figyelembe a verziója, ebben a példában "1.0.0.20170815.3".

![Service Fabric Explorer][sfx1]

## <a name="update-the-application"></a>Az alkalmazás frissítése
Az alkalmazás kódja módosíthatók.  Mentse, és véglegesítse a módosításokat, az előző lépések.

Az alkalmazás a frissítés megkezdése után a frissítési folyamat állapotát a Service Fabric Explorer nézheti meg:

![Service Fabric Explorer][sfx2]

Az alkalmazásfrissítés több percig is eltarthat. Ha a frissítés befejeződött, az alkalmazást futtatja a következő verziójára.  Ebben a példában "1.0.0.20170815.4".

![Service Fabric Explorer][sfx3]

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A verziókövetési rendszerrel hozzáadása a projekthez
> * A build definíció létrehozása
> * Egy kiadási definíció létrehozása
> * Automatikus központi telepítése és alkalmazás frissítése

Előzetes következő oktatóanyagot:
> [!div class="nextstepaction"]
> [Figyelés és diagnosztika az alkalmazás beállítása](service-fabric-tutorial-monitoring-aspnet.md) 


<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
