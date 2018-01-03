---
title: "Telepítheti a virtuálisgép-méretezési készlet Visual Studio használatával |} Microsoft Docs"
description: "Központi telepítése a Visual Studio és a Resource Manager-sablon használatával virtuálisgép-méretezési csoportok"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73454abc11a832a1b7f4131bf13699bd0a94edea
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>A virtuálisgép-méretezési készlet létrehozása a Visual Studio
Ez a cikk bemutatja, hogyan telepítheti egy Azure virtuális gépek méretezési csoportján Visual Studio erőforrás csoport központi telepítéssel.

[Az Azure virtuálisgép-méretezési csoportok](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) olyan Azure számítási erőforrás telepítéséhez és a hasonló virtuális gépek automatikus méretezése a gyűjteményeinek kezelését és a terheléselosztás. Kioszthatja és központi telepítése használatával virtuálisgép-méretezési csoportok [Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates). Az Azure Resource Manager-sablonok az Azure parancssori felület, PowerShell, REST telepíthető és is közvetlenül a Visual Studio. A Visual Studio számos példa sablonok, amelyek az Azure erőforrás-csoport központi telepítésének projektek részeként telepíthetők.

Azure-erőforráscsoport központi telepítéseket, amelyek egy csoportot, és tegye közzé a kapcsolódó Azure-erőforrások olyan készletét egyetlen központi telepítési művelettel. További róluk itt: [létrehozása és telepítése a Visual Studio használatával Azure erőforráscsoport-sablonok a](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Előfeltételek
A Visual Studio virtuálisgép-méretezési csoportok üzembe helyezésének első lépései, a következőkre lesz szüksége:

* A Visual Studio 2013 vagy újabb verzió
* Az Azure SDK 2.7, 2.8 vagy 2.9

>[!NOTE]
>Ezek az utasítások azt feltételezik, hogy a Visual Studio használata [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>A projekt létrehozása
1. Hozzon létre egy új projektet a Visual Studio kiválasztásával **fájl |} Új |} Projekt**.
   
    ![Új fájl][file_new]

2. A **Visual C# |} Felhő**, válassza a **Azure Resource Manager** projekt telepítéséhez az Azure Resource Manager-sablon létrehozásához.
   
    ![Projekt létrehozása][create_project]

3. A sablonok listáján válassza ki a Linux vagy a Windows virtuális gép méretezési beállítása sablon.
   
   ![Sablon kiválasztása][select_Template]

4. A projekt létrehozása után megjelenik PowerShell telepítési parancsfájlok, az Azure Resource Manager-sablon vagy egy paraméterfájl a virtuálisgép-méretezési készlet.
   
    ![Megoldáskezelő][solution_explorer]

## <a name="customize-your-project"></a>A projekt testreszabása
Most szerkesztheti a sablont tartozó, az alkalmazás igényeinek, például a Virtuálisgép-bővítmény tulajdonságai hozzáadása vagy szerkesztése terheléselosztási szabályok. Alapértelmezés szerint a központi telepítése a AzureDiagnostics bővítményt, amely megkönnyíti az automatikus skálázási szabályok hozzáadása a virtuális gép méretezési beállítása sablonok vannak konfigurálva. Emellett központilag telepíti a terheléselosztó a nyilvános IP-cím, a bejövő NAT-szabályok konfigurálva. 

A load balancer amellyel SSH (Linux) vagy RDP (Windows) a Virtuálisgép-példányok csatlakozhat. Az előtér-porttartomány 50000 kezdődik. Ez azt jelenti, hogy ha Linux SSH port 50000, hogy meg legyenek átirányítva a méretezési csoportban lévő első VM 22-es portot. Kapcsolódás port 50001 irányítása 22-es portot a második virtuális gép és így tovább.

 Egy jó módszer a Visual Studio a sablonok szerkesztése, hogy a JSON-vázlat használja a paraméterek, a változók és az erőforrások rendszerezéséhez. Megismerhesse a séma, a Visual Studio felhívhatják a sablonban hibák telepítése előtt.

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>A projekt telepítése
1. A virtuálisgép-méretezési csoport erőforrás létrehozása az Azure Resource Manager-sablon telepítése. Kattintson a jobb gombbal a projektcsomópontra, és válassza a **telepítés |} Új központi telepítési**.
   
    ![Sablon üzembe helyezése][5deploy_Template]
    
2. A "Központi telepítése az erőforráscsoport" párbeszédpanelen jelölje ki az előfizetését.
   
    ![Sablon üzembe helyezése][6deploy_Template]

3. Itt létrehozhat egy Azure-erőforráscsoportot, hogy a sablon telepítéséhez.
   
    ![Új erőforráscsoport][new_resource]

4. Ezután kattintson **paraméterek szerkesztése** a sablon átadott paraméterek. Az operációs rendszer, amely azonban szükséges a központi telepítés létrehozásához adja meg a felhasználónevet és jelszót. Ha nincs PowerShell eszközök telepített Visual Studio, javasoljuk, hogy ellenőrizze **menthetik a jelszavakat** elkerülése érdekében a rejtett PowerShell parancssorba, vagy használjon [keyvault támogatási](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Paraméterek szerkesztése][edit_parameters]

5. Most kattintson **telepítés**. A **kimeneti** ablak a központi telepítés végrehajtási állapotát tartalmazza. Vegye figyelembe, hogy a művelet végrehajtása történik a **telepítés-AzureResourceGroup.ps1** parancsfájl.
   
   ![Kimeneti ablak][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport felfedezése
Miután a telepítés befejeződik, megtekintheti az új virtuálisgép-méretezési csoportban a Visual Studio **Cloud Explorer** (frissítse a listát). Cloud Explorer lehetővé teszi a Visual Studio Azure-erőforrások kezeléséhez alkalmazások fejlesztése során. Megtekintheti a virtuális gép méretezési csoportban lévő a [Azure-portálon](https://portal.azure.com) és [Azure erőforrás-kezelő](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 A portál biztosítja a legjobb módszer az Azure infrastruktúra egy webböngészővel rendelkező vizuálisan kezelését, míg jogosultságot ad az ablak azokat a "példányait tartalmazó nézetet" és a PowerShell-parancsokat is megjelenítő szolgál az Azure erőforrás-kezelő egyszerűen megismerheti és hibakeresése az Azure-erőforrások, az erőforrások nézi.

## <a name="next-steps"></a>További lépések
Miután sikeresen telepítése a Visual Studio alkalmazással virtuálisgép-méretezési csoportok, a projekt alkalmazás igényeinek még jobban testre szabható. Például állítsa be automatikus méretezése hozzáadásával egy **Insights** erőforrás, infrastruktúra ad hozzá a sablont, (például önálló virtuális gépek), vagy az egyéni parancsprogramok futtatására szolgáló bővítmény használatával alkalmazások központi telepítéséhez. Jó példa sablonok találhatók a [Azure gyors üzembe helyezési sablonokat](https://github.com/Azure/azure-quickstart-templates) GitHub-adattár ("vmss" keresése).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
