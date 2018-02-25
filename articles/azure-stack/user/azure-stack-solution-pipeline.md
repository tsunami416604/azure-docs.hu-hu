---
title: "Telepítse az alkalmazást az Azure és az Azure verem |} Microsoft Docs"
description: "Megtudhatja, hogyan telepíthet alkalmazásokat az Azure és az Azure-verem és a hibrid CI/CD."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 6292a846a2c3d7e112558ef0d2b62b3a3fdd3c51
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Alkalmazások telepítése Azure és az Azure verem
*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

A hibrid [folyamatos integrációt](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[folyamatos kézbesítési](https://www.visualstudio.com/learn/what-is-continuous-delivery/)(CI/CD) folyamat lehetővé teszi, hogy hozhat létre, tesztelheti és az alkalmazás üzembe helyezése több felhők.  Ebben az oktatóanyagban egy minta környezetben megtudhatja, hogyan egy hibrid CI/CD folyamat segítségével hoz létre:
 
> [!div class="checklist"]
> * Indítsa el a kód véglegesíti a Visual Studio Team Services (VSTS) tárházhoz alapján új buildverziót.
> * Automatikusan az újonnan beépített kód telepítése az Azure-bA a felhasználói tesztelés.
> * Miután a kód ment át tesztelésen, automatikusan telepítheti őket Azure verem. 


## <a name="prerequisites"></a>Előfeltételek
Néhány összetevőt egy hibrid CI/CD folyamat létrehozásához szükségesek, és előkészítéséhez hosszabb időt vehet igénybe.  Ha már rendelkezik néhány ezeket az összetevőket, győződjön meg arról, hogy azok megfelelnek megkezdése előtt.

Ez a témakör feltételezi, hogy rendelkezik-e bizonyos Azure és az Azure-verem ismerete. Ha azt szeretné, további, a folytatás előtt mindenképpen indítsa el az alábbi témakörök:

- [Bevezetés az Azure-bA](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Az Azure verem kulcsfogalmak](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
 - Hozzon létre egy [webalkalmazás](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md), és az [az FTP-közzététel](../../app-service/app-service-deploy-ftp.md).  Jegyezze meg az új webes alkalmazás URL-CÍMÉT, mert a rendszer később.


### <a name="azure-stack"></a>Azure Stack
 - [Az Azure verem telepítése](../azure-stack-run-powershell-script.md).  A telepítés általában befejeződik, ezért ennek megfelelően tervezheti néhány órát vesz igénybe.
 - Telepítése [App Service](../azure-stack-app-service-deploy.md) Azure verem PaaS-szolgáltatások.
 - Webalkalmazás létrehozása és az [az FTP-közzététel](../azure-stack-app-service-enable-ftp.md).  Jegyezze meg az új webes alkalmazás URL-CÍMÉT, mert a rendszer később.  

### <a name="developer-tools"></a>Fejlesztői eszközök
 - Hozzon létre egy [VSTS munkaterület](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).  A regisztrációs folyamat létrehoz egy "MyFirstProject." nevű projekt  
 - [Telepítse a Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) és [bejelentkezés az VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - A projekt csatlakozni és [helyileg klónozni](https://www.visualstudio.com/docs/git/gitquickstart).
 - Hozzon létre egy [ügynök készlet](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues) a VSTS.
 - Visual Studio telepítése és központi telepítése egy [VSTS build ügynök](https://www.visualstudio.com/docs/build/actions/agents/v2-windows) Azure veremben levő virtuális géphez. 
 

## <a name="create-app--push-to-vsts"></a>Alkalmazás létrehozása & VSTS leküldése

### <a name="create-application"></a>Alkalmazás létrehozása
Ebben a szakaszban egy egyszerű ASP.NET-alkalmazás létrehozása, és hogy VSTS.  Ezeket a lépéseket a normál fejlesztői munkafolyamat jelöl, és fejlesztői eszközök és nyelvek módosítani kell. 

1.  Nyissa meg a Visual Studiót.
2.  A csapat Explorer területről és **megoldások...**  területen kattintson a **új**.
3.  Válassza ki **Visual C#** > **webes** > **ASP.NET webes alkalmazás (.NET-keretrendszer)**.
4.  Nevezze el a alkalmazást, majd kattintson a **OK**.
5.  A következő képernyőn, hagyja az alapértelmezett érték (Web Forms keretrendszerre), és kattintson a **OK**.

### <a name="commit-and-push-changes-to-vsts"></a>Véglegesítse és változásainak leküldése VSTS
1.  Válassza ki a legördülő listában, majd kattintson a Visual Studio Team Intéző segítségével **módosítások**.
2.  Adja meg a véglegesítési üzenetet, és válassza ki **véglegesíti az összes**. A fájl mentése, kattintson az Igen gombra menti az összes kérheti.
3.  Miután véglegesített, Visual Studio szinkronizálni a módosításokat a projekthez kínál. Válassza a **Szinkronizálás** elemet.

    ![a véglegesítési képernyőt ábrázoló, amikor befejeződött a véglegesítési kép](./media/azure-stack-solution-pipeline/image1.png)

4.  A szinkronizálás lapon alatt *kimenő*, megjelenik az új véglegesítési.  Válassza ki **leküldéses** VSTS módosítása szinkronizálásához.

    ![kép megjelenítése szinkronizálási lépéseit](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>Tekintse át a VSTS kódot
Egyszer már véglegesítve lett a változás, és leküldeni VSTS, ellenőrizze a kódot a VSTS-portálról.  Válassza ki **kód**, majd **fájlok** a legördülő menüből.  A létrehozott megoldás tekintheti meg.

## <a name="create-build-definition"></a>Build definíció létrehozása
A létrehozási folyamat határozza meg, hogyan az alkalmazás összeállítása és minden véglegesítés kódmódosításokat központi telepítésével kapcsolatos csomagolva. A fenti példában használjuk a belefoglalt sablon konfigurálása a felépítési folyamat ASP.NET alkalmazás, bár lehet, hogy ez a konfiguráció igazítani, attól függően, hogy az alkalmazás.

1.  Jelentkezzen be a VSTS munkaterület egy webböngészőből.
2.  Válassza ki a szalagcím **Build & kiadási** , majd **buildek**.
3.  Kattintson a **+ új definition**.
4.  Válassza a sablonok listájának, **ASP.NET (előzetes verzió)** válassza **alkalmaz**.
5.  Módosítsa a *MSBuild-argumentumok* mezőjét *megoldás fordítása* lépéseket:

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  Válassza ki a **beállítások** fülre, és válassza ki az ügynök várólista a build Agent telepített Azure veremben levő virtuális géphez. 
7.  Válassza ki a **eseményindítók** lapot, és engedélyezze **folyamatos integrációt**.
7.  Kattintson a **mentés és a feldolgozási sor** , és válassza **mentése** a legördülő listából. 

## <a name="create-release-definition"></a>Kiadás definíció létrehozása
A kiadási folyamat határozza meg, hogyan vannak telepítve az előző lépésben buildek környezetbe.  Ebben az oktatóanyagban az Azure Web Apps FTP az ASP.NET-alkalmazás közzététele azt. Az Azure-bA kiadási konfigurálásához használja az alábbi lépéseket:

1.  Válassza ki a VSTS szalagcím **Build & kiadási** , majd **kiadásokban**.
2.  Kattintson a zöld **+ új definition**.
3.  Válassza ki **üres** kattintson **következő**.
4.  Jelölje be a *folyamatos üzembe helyezés*, és kattintson a **létrehozása**.

Most, hogy egy üres kiadás definíciója elkészítette és azt a build kötődik, jelenleg lépései az Azure környezetbe felvenni:

1.  Kattintson a zöld  **+**  feladatok hozzáadásához.
2.  Válassza ki **összes**, majd a listáról, **FTP feltöltése** válassza ki **Bezárás**.
3.  Válassza ki a **FTP feltöltése** feladat, az előzőekben adott hozzá, és konfigurálja a következő paramétereket:
    
    | Paraméter | Érték |
    | ----- | ----- |
    |Hitelesítési módszer| Adja meg hitelesítő adatokat|
    |Kiszolgáló URL-címe | Az Azure portálról lekért webes alkalmazás FTP URL-címe |
    |Felhasználónév | Felhasználónév FTP hitelesítő adatok webalkalmazás létrehozásakor beállított |
    |Jelszó | Webalkalmazás FTP hitelesítő adatok létrehozásakor létrehozott jelszó|
    |Forráskönyvtár | $(System.DefaultWorkingDirectory)\**\ |
    |Távoli könyvtár | /Site/wwwroot / |
    |Megőrizheti a fájlok elérési útja | Engedélyezve van (be van jelölve)|

4.  Kattintson a **mentése**

Végül a kiadási-definíció, használja az ügynök a az ügynök telepítése az alábbi lépéseket követve tartalmazó konfigurálja:
1.  Válassza ki a kiadási definícióját, és kattintson a **szerkesztése**.
2.  Válassza ki **ügynökön** a középső oszlopból.  A jobb oldali oszlopban válassza ki a futó Azure veremben build ügynököt tartalmazó ügynök várólista.  
    ![kép ábrázoló konfigurációs kiadás definíció konkrét várólistába helyezi használata](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Az alkalmazás telepítése az Azure-bA
Ebben a lépésben a újonnan beépített CI/CD folyamat használja az ASP.NET alkalmazás központi telepítése egy Azure-webalkalmazásban. 

1.  A szalagcím a VSTS, válassza ki **Build & kiadási**, majd válassza ki **buildek**.
2.  Kattintson a **...**  korábban létrehozott, majd válassza a build definition **új build várólistára**.
3.  Fogadja el az alapértelmezett beállításokat, és kattintson a **Ok**.  A build kezdődik, és végrehajtási jeleníti meg.
4.  A létrehozás befejezése után, nyomon követheti a állapot kiválasztásával **Build & kiadási** választja **kiadásokban**.
5.  A létrehozás befejezése után, látogasson el a webhely, az a webalkalmazás létrehozásakor az áttelepítés előtt feljegyzett URL-cím segítségével.    


## <a name="add-azure-stack-to-pipeline"></a>Adja hozzá az Azure verem-feldolgozási folyamat
Most, hogy a CI/CD folyamat tesztelését az Azure-bA üzembe helyezésével, azt az Azure-verem hozzáadása a feldolgozási sor idő.  Az alábbi lépéseket hozzon létre egy új környezetet, és adja hozzá az FTP feltöltése feladatot az alkalmazás telepítése az Azure-verem.  Egy kiadási jóváhagyó, amely szimulálása "off" meg bejelentkezni Azure verem kód kiadási úgy is vegye fel.  

1.  Válassza ki a kiadási-definícióban **+ Hozzáadás környezet** és **hozzon létre új környezet**.
2.  Válassza ki **üres**, kattintson a **következő**.
3.  Válassza ki **bizonyos felhasználók** , és adja meg a fiókját.  Kattintson a **Létrehozás** gombra.
4.  Nevezze át a környezet a meglévő nevét, és írja be *Azure verem*.
5.  Kiválasztása az Azure-verem környezetben, majd jelölje ki, **tevékenységek hozzáadása az**.
6.  Válassza ki a **FTP feltöltése** feladatot, és válassza ki **Hozzáadás**, majd jelölje be **Bezárás**.


### <a name="configure-ftp-task"></a>FTP-tevékenység konfigurálása
Most, hogy a létrehozott kiadási, konfigurálhatja a közzététel a webalkalmazás Azure veremben szükséges lépéseket.  Az FTP-feltöltési feladat konfigurálta az Azure-ba, ahogy a feladat konfigurálása Azure verem:

1.  Válassza ki a **FTP feltöltése** feladat, az előzőekben adott hozzá, és konfigurálja a következő paramétereket:
    
    | Paraméter | Érték |
    | -----     | ----- |
    |Hitelesítési módszer| Adja meg hitelesítő adatokat|
    |Kiszolgáló URL-címe | Verem Azure portálról lekért webes alkalmazás FTP URL-címe |
    |Felhasználónév | Felhasználónév FTP hitelesítő adatok webalkalmazás létrehozásakor beállított |
    |Jelszó | Webalkalmazás FTP hitelesítő adatok létrehozásakor létrehozott jelszó|
    |Forráskönyvtár | $(System.DefaultWorkingDirectory)\**\ |
    |Távoli könyvtár | /Site/wwwroot /|
    |Megőrizheti a fájlok elérési útja | Engedélyezve van (be van jelölve)|

2.  Kattintson a **mentése**

Végül adja meg a kiadási-definíció, használja az ügynök a az ügynök telepítése az alábbi lépéseket követve tartalmazó:
1.  Válassza ki a kiadási definícióját, és kattintson a **szerkesztése**
2.  Válassza ki **ügynökön** a középső oszlopból. A jobb oldali oszlopban válassza ki a futó Azure veremben build ügynököt tartalmazó ügynök várólista.  
    ![kép ábrázoló konfigurációs kiadás definíció konkrét várólistába helyezi használata](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>Új kód telepítése
A hibrid CI/CD sorban, Azure verem közzétételi végső lépése most tesztelheti.  Ebben a szakaszban módosítsa a hely élőláb, és indítsa el a központi telepítés a feldolgozási folyamaton keresztül.  Művelet befejeződése után megjelenik a telepített Azure tekintse át a módosításokat, majd a kiadás jóváhagyása után azok közzététele megtörténik Azure verem.

1. A Visual Studióban nyissa meg a *site.master* fájlt, és módosítsa a sort:
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    módosítsa a következőre:

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  A módosítások véglegesítéséhez és VSTS szinkronizálja.  
4.  A VSTS-munkaterület a állapotának build kiválasztásával **Build & kiadási** > **összeállítása**
5.  A folyamatban lévő build jelenik meg.  Kattintson duplán az állapot, és a létrehozási folyamat figyelemmel követheti.  Után a konzol "Végzett build" jelenik meg, helyezze át ellenőrizze a kiadási **Build & kiadási** > **kiadási**.  Kattintson duplán a kiadásban.
6.  Egy kiadási felülvizsgálatot igényel értesítést fog kapni. Ellenőrizze a webes alkalmazás URL-CÍMÉT, és az új módosítások találhatók.  Hagyja jóvá a VSTS kiadást.
    ![kép ábrázoló konfigurációs kiadás definíció konkrét várólistába helyezi használata](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Győződjön meg arról, Azure verem közzétételt a webalkalmazás létrehozásakor az áttelepítés előtt feljegyzett URL-cím segítségével webhelyén befejeződött.
    ![ASP ábrázoló kép Nettó alkalmazás élőláb megváltozott](./media/azure-stack-solution-pipeline/image5.png)


Más hibrid felhő mintára építőelemként most már használhatja az új hibrid CI/CD folyamat.

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy hibrid CI/CD-feldolgozási:

> [!div class="checklist"]
> * Kezdeményezi a kód alapján új buildverziót véglegesíti a Visual Studio Team Services (VSTS) tárházba.
> * A felhasználói tesztelés automatikusan telepíti az újonnan beépített kód Azure.
> * Miután a kód ment át tesztelésen, automatikusan telepíti a Azure verem. 

Most, hogy egy hibrid CI/CD folyamatot, folytassa a hogyan fejleszthetnek alkalmazásokat az Azure-verem módját.

> [!div class="nextstepaction"]
> [Fejlesztés az Azure Stackhez](azure-stack-developer.md)


