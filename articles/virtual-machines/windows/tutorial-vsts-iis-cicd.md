---
title: Az oktatóanyag - CI/CD folyamatokat létrehozni az Azure-ban Team Services |} Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan hozzon létre egy Visual Studio Team Services folyamat folyamatos integrációt és kézbesítését, hogy egy webes alkalmazást telepíti egy Windows Azure-ban az IIS.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d017f2453bbd757c16e2df034f5879f24ffe42f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Oktatóanyag: Hozzon létre egy folyamatos integrációt folyamat Visual Studio Team Services és az IIS
A build, a vizsgálati és a telepítési fázisai alkalmazásfejlesztés automatizálását, használhatja a folyamatos integrációt és a központi telepítés (CI/CD) folyamat. Ebben az oktatóanyagban létrehoz egy Visual Studio Team Services és a Windows rendszerű virtuális gép (VM) használata az IIS-t futtató Azure CI/CD folyamat. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Team Services projektbe ASP.NET webes alkalmazás közzététele
> * A kód véglegesítések által elindított build-definíció létrehozása
> * IIS telepítése és konfigurálása az Azure virtuális gépen
> * Az IIS-példány hozzáadása a Team Services telepítési csoporthoz
> * Hozzon létre egy kiadási közzététele az új webes API-definíciót az IIS-csomagok központi telepítése
> * A CI/CD-feldolgozási folyamat tesztelése

Ebben az oktatóanyagban az Azure PowerShell modul verziója 5.7.0 szükséges vagy újabb. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.


## <a name="create-project-in-team-services"></a>A Team Services-projekt létrehozása
A Visual Studio Team Services lehetővé teszi, hogy könnyen együttműködés és fejlesztési egy helyszíni-felügyeleti megoldás fenntartása nélkül. Team Services biztosít a felhő kód tesztelése, elkészítéséhez és az application insights. Választhat egy verzió vezérlő tárház és IDE a kód fejlesztési legjobban illő. Ebben az oktatóanyagban a egy ingyenes fiókot hozhat létre egy egyszerű ASP.NET-webalkalmazás és CI/CD folyamat. Ha még nem rendelkezik egy Team Services-fiók [hozzon létre egyet](http://go.microsoft.com/fwlink/?LinkId=307137).

Kód véglegesítési folyamat kezeléséhez, létre definíciókat és meghatározások kiadási, hozzon létre egy projekt Team Services az alábbiak szerint:

1. A Team Services irányítópult megnyitása a böngészőben, és válassza a **új projekt**.
2. Adja meg *myWebApp* a a **projektnevet**. Minden más alapértelmezett értéket használatához hagyja *Git* verziókezelést és *Agile* munkaelemet bekérő folyamat.
3. Lehetőséget **megosztása** *csapattagok*, majd jelölje be **létrehozása**.
5. Miután létrejött a projekt, válassza a beállítást, ha **információs vagy gitignore inicializálása**, majd **inicializálása**.
6. Válassza ki az új projekt belül **irányítópultok** tetején, majd válassza ki **Megnyitás Visual Studio**.


## <a name="create-aspnet-web-application"></a>ASP.NET-webalkalmazás létrehozása
Az előző lépésben létrehozott csapat szolgáltatásokban projekt. Az utolsó lépést az új projektet a Visual Studio nyílik meg. A kód véglegesíti a kezelheti a **Team Explorer** ablak. Hozzon létre az új projekt helyi példányát, majd egy ASP.NET-webalkalmazás létrehozása sablonból az alábbiak szerint:

1. Válassza ki **Klónozás** egy helyi git-tárház a Team Services projekt létrehozásához.
    
    ![Team Services projektből tárház klónozása](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. A **megoldások**, jelölje be **új**.

    ![Webalkalmazási megoldás létrehozása](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Válassza ki **webes** sablonok, majd válassza a **ASP.NET Web Application** sablont.
    1. Írjon be egy nevet az alkalmazásnak, például *myWebApp*, és törölje a jelet a jelölőnégyzetből **könyvtár létrehozása a megoldáshoz**.
    2. Ha a beállítás érhető el, törölje a bejelölését **Application Insights hozzáadása a projekthez**. Az Application Insights használatához engedélyezze a webalkalmazás az Azure Application insights szolgáltatással. Biztosítható, hogy ez az oktatóanyag az egyszerű, hagyja ki ezt a folyamatot.
    3. Kattintson az **OK** gombra.
4. Válasszon **MVC** a sablon listából.
    1. Válassza ki **hitelesítés módosítása**, válassza a **nem hitelesítési**, majd jelölje be **OK**.
    2. Válassza ki **OK** a megoldás létrehozásához.
5. Az a **Team Explorer** ablakban válasszon **módosítások**.

    ![Git-tárház Team Services helyi módosításainak véglegesítése](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. A véglegesítési szövegmezőben adjon meg egy üzenetet, mint *kezdeti véglegesítési*. Válasszon **véglegesíti az összes és a szinkronizálási** a legördülő menüből.


## <a name="create-build-definition"></a>Build definíció létrehozása
A Team Services segítségével build definíció helyzeteit vázolják fel, hogyan kell kialakítani, az alkalmazás. Ebben az oktatóanyagban létrehozhatunk olyan alapvető definíciót, amely a vesz igénybe a forráskódban, a megoldás alkot, majd hoz létre a webkiszolgáló az IIS-kiszolgáló a webalkalmazás futtatásához használhatja azt csomag központi telepítéséhez.

1. A Team Services projekthez, válassza a **Build & kiadási** tetején, majd válassza ki **buildek**.
3. Válassza ki **+ új definition**.
4. Válassza ki a **ASP.NET (előzetes verzió)** sablont, és válassza **alkalmaz**.
5. Hagyja bejelölve az alapértelmezett feladat értékeket. A **adatforrások beolvasása**, ügyeljen arra, hogy a *myWebApp* tárház és *fő* fiókirodai van kiválasztva.

    ![Build definíció Team Services-projekt létrehozása](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Az a **eseményindítók** lapján húzza a csúszkát a **engedélyezése ehhez az eseményindítóhoz** való *engedélyezve*.
7. Mentse a build definíció- és várólista új buildverziót kiválasztásával **mentés és a feldolgozási sor** , majd **mentés és a feldolgozási sor** újra. Hagyja meg az alapértelmezett beállításokat, és válassza ki **várólista**.

Tekintse meg a build üzemeltetett ügynökön van ütemezve, majd megkezdi. A kimenet a következő példához hasonló:

![A Team Services projekt sikeres build](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Virtuális gép létrehozása
Adjon meg egy platform, az ASP.NET webalkalmazás futtatásához, IIS-t futtató Windows rendszerű virtuális gép van szükség. Vonja össze a szolgáltatások által használt kommunikál az IIS-példány, a kódot és buildek véglegesíti az ügynök által kiváltott.

Windows Server 2016 virtuális gép létrehozása az [új AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* a a *USA keleti régiója* helyét. Az erőforráscsoport *myResourceGroupVSTS* és a támogató hálózati erőforrások is jönnek létre. Webes forgalomban, az TCP-port engedélyezéséhez *80* a virtuális gép meg van nyitva. Amikor a rendszer kéri, adja meg a felhasználónevet és jelszót, amely a bejelentkezési hitelesítő adatok a virtuális gép használható:

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupVSTS" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

Csatlakoztassa a virtuális Gépet, szerezze be a nyilvános IP-cím [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) az alábbiak szerint:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

A virtuális Gépet egy távoli asztali munkamenetet létrehozni:

```cmd
mstsc /v:<publicIpAddress>
```

Nyissa meg a virtuális gép, egy **rendszergazda PowerShell** parancssort. Az IIS és a kötelező .NET-szolgáltatások telepítése a következőképpen:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Üzembe helyezési csoport létrehozása
Leküldéses a web deploy csomag az IIS-kiszolgálóra, megadhat egy üzembe helyezési csoport Team Services. Ehhez a csoporthoz adja meg, melyik kiszolgálókat új buildek célját kód Team Services véglegesíti és buildek elvégzése teszi lehetővé.

1. Team Services, válassza a **Build & kiadási** , és válassza **telepítési csoportban**.
2. Válasszon **központi telepítés hozzáadása csoporthoz**.
3. Írjon be egy nevet a csoportnak, például *myIIS*, majd jelölje be **létrehozása**.
4. Az a **gépek regisztrálása** területen győződjön meg arról *Windows* van kiválasztva, majd jelölje be a jelölőnégyzetet annak **személyes hozzáférési jogkivonat a parancsfájl a hitelesítéshez használandó**.
5. Válassza ki **parancsfájl másolása a vágólapra**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Az IIS virtuális gép hozzáadása a központi telepítési csoport
A létrehozott telepítési csoport minden egyes IIS-példány felvétele a csoportba. Team Services hoz létre olyan parancsfájlt, amely letölti és az ügynök konfigurálja a virtuális Gépet, amely megkapja az új webes csomagok központi telepítése, majd az IIS vonatkozik.

1. Vissza a **rendszergazda PowerShell** munkamenet a virtuális gépen, illessze be, és futtassa a parancsprogramot másolta át Team Services.
2. Ha az ügynök konfigurálását a címkék kéri, válassza *Y* , és írja be *webes*.
3. Ha a rendszer kéri a felhasználói fiók, nyomja le az *vissza* elfogadja az alapértelmezett beállításokat.
4. A parancsfájl üzenetet befejezésére való várakozás *szolgáltatás vstsagent.account.computername sikeresen elindult*.
5. Az a **telepítési csoportban** oldalán a **Build & kiadási** menüben nyissa meg a *myIIS* üzembe helyezési csoport. Az a **gépek** lapra, győződjön meg arról, hogy a virtuális gép.

    ![Virtuális gép Team Services üzembe helyezési csoport sikeresen hozzáadva](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Kiadás definíció létrehozása
A buildek közzétételéhez létre kiadás definícióját Team Services. Ez a definíció sikeres build az alkalmazás automatikusan elindul. Úgy dönt, hogy a központi telepítési csoportot, majd a webalkalmazás-csomag telepítése, valamint a megfelelő IIS beállításait.

1. Válasszon **Build & kiadási**, majd jelölje be **buildek**. Válassza ki az előző lépésben létrehozott build-definícióban.
2. A **nemrég befejeződött**, válassza ki a legutóbbi összeállítása, majd válassza ki **kiadás**.
3. Válasszon **Igen** kiadás definíció létrehozásához.
4. Válassza ki a **üres** sablont, majd válassza ki **következő**.
5. Ellenőrizze, hogy a projekt és a forrás build definíciója a rendszer feltölti a projektet.
6. Válassza ki a **folyamatos üzembe helyezés** jelölje be a jelölőnégyzetet, majd válasszon **létrehozása**.
7. A legördülő listában jelölje be a **+ Hozzáadás feladatok** válassza *adja hozzá a központi telepítési csoport szakaszok*.
    
    ![Adja hozzá a feladatot a kibocsátási Team Services-definíciójában](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Válasszon **Hozzáadás** melletti **IIS Web App Deploy(Preview)**, majd jelölje be **Bezárás**.
9. Válassza ki a **futhat az üzembe helyezési csoport** Szülőtevékenység.
    1. A **üzembe helyezési csoport**, válassza ki a korábban létrehozott telepítési csoport például *myIIS*.
    2. Az a **számítógép-címkék** mezőben válassza **Hozzáadás** , és válassza a *webes* címke.
    
    ![Kiadás definition telepítési csoport feladat az IIS-hez](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Válassza ki a **központi telepítése: az IIS webes alkalmazás központi telepítése** beállítása az IIS-példány beállításokat az alábbiak szerint:
    1. A **webhelynevet**, adja meg *alapértelmezett webhely*.
    2. Hagyja meg a többi alapértelmezett beállítást.
12. Válasszon **mentése**, majd jelölje be **OK** kétszer.


## <a name="create-a-release-and-publish"></a>Egy kiadási létrehozása és közzététele
Most tolható ki a webhely egy új kiadási csomag telepítéséhez. Ez a lépés kommunikál az ügynök, amely a központi telepítési csoport része, a leküldéses értesítések a webes összes példányt csomag telepítése, majd konfigurálja az IIS a frissített webalkalmazás futtatásához.

1. A kiadási definíciót, válassza ki **+ kiadási**, majd válassza ki **létrehozása kiadási**.
2. Győződjön meg arról, hogy a legújabb buildjével van kijelölve a legördülő listából válassza ki, valamint **automatikus központi telepítési: kiadás létrehozása után**. Kattintson a **Létrehozás** gombra.
3. Egy kis szalagcím akkor jelenik meg a kiadási definition tetején, mint *kiadás kibocsátási-1 létrejött*. Jelölje ki a kiadási kapcsolatot.
4. Nyissa meg a **naplók** fülre, és tekintse meg a kiadási folyamatban van.
    
    ![Sikeres Team Services kiadás és webes telepítési csomag leküldéses](media/tutorial-vsts-iis-cicd/successful_release.png)

5. A kiadási befejezése után nyisson meg egy webböngészőt, és adja meg a virtuális gép nyilvános KVI címét. Az ASP.NET-webalkalmazás fut.

    ![ASP.NET webalkalmazás az IIS virtuális gépen](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>A teljes CI/CD-feldolgozási folyamat tesztelése
A webes alkalmazás IIS-kiszolgálón fut, és próbálja meg a teljes CI/CD-feldolgozási folyamat. Után módosítja a Visual Studio és akkor váltódik ki, a kódot, a build véglegesítési, majd elindítja a frissített Web kiadási csomag központi telepítéséhez az IIS-hez:

1. A Visual Studióban nyissa meg a **Megoldáskezelőben** ablak.
2. Keresse meg, és nyissa meg a *myWebApp |} Nézetek |} Kezdőlap |} Index.cshtml*
3. Sor olvasására 6 szerkesztése:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Mentse a fájlt.
5. Nyissa meg a **Team Explorer** ablakban válassza ki a *myWebApp* projektre, majd válassza a **módosítások**.
6. Adja meg például a véglegesítési üzenetet, *tesztelés CI/CD csővezeték*, majd válassza ki **véglegesítési összes és a szinkronizálási** a legördülő menüből.
7. Team Services munkaterület egy új build akkor váltódik ki, a kód véglegesítése. 
    - Válasszon **Build & kiadási**, majd jelölje be **buildek**. 
    - Válasszon a build-definíciót, majd válassza ki a **várakozik & futó** buildet bemutató a build előrehaladtával.
9. Ha a build sikeres, egy új kiadási elindul.
    - Válassza ki **Build & kiadási**, majd **kiadásokban** központi telepítése az IIS virtuális gép leküldve csomag webes megjelenítéséhez. 
    - Válassza ki a **frissítése** állapotának frissítése ikonra. Ha a *környezetek* oszlopban egy zöld pipa látható, a kiadás sikeresen telepítette az IIS-hez.
11. Az alkalmazott módosítások megtekintéséhez frissítse az IIS-webhely a böngészőben.

    ![CI/CD láncból az IIS virtuális gépen futó ASP.NET-webalkalmazás](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy ASP.NET-webalkalmazás létrehozása a Team Services és build konfigurálása, és kiadás definícióit új webes telepítendő csomagok központi telepítése az IIS minden kód érvényesítéskor. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Team Services projektbe ASP.NET webes alkalmazás közzététele
> * A kód véglegesítések által elindított build-definíció létrehozása
> * IIS telepítése és konfigurálása az Azure virtuális gépen
> * Az IIS-példány hozzáadása a Team Services telepítési csoporthoz
> * Hozzon létre egy kiadási közzététele az új webes API-definíciót az IIS-csomagok központi telepítése
> * A CI/CD-feldolgozási folyamat tesztelése

Megtudhatja, hogyan telepíthet egy SQL következő oktatóanyagot előzetes&#92;IIS&#92;Windows virtuális gépek két .NET tömbhöz.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET stack](tutorial-iis-sql.md)