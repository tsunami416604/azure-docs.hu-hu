---
title: Üzembe helyezése a Virtual Machine Scale Set Visual studióval |} A Microsoft Docs
description: Virtual Machine Scale Sets használatával a Visual Studio és a egy Resource Manager-sablon üzembe helyezése
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741422"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Egy virtuálisgép-méretezési csoportban létrehozása a Visual Studióval
Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure Virtual Machine Scale Set használatával a Visual Studio erőforráscsoport üzemelő példányához.

[Az Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) van olyan Azure Compute-erőforrások üzembe helyezése és az automatikus skálázás hasonló virtuális gépek gyűjteményét kezelheti és terheléselosztási funkciók. Építse ki, és üzembe helyezése a Virtual Machine Scale Sets használatával [Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates). Az Azure Resource Manager-sablonok is üzembe helyezhetők az Azure CLI, PowerShell, REST használatával, és emellett közvetlenül a Visual Studióból. A Visual Studio biztosít, például sablonokat, amelyek egy Azure erőforráscsoport-telepítés projekt részeként telepíthető.

Azure-erőforráscsoport központi telepítések, amelyek egy csoportot, és tegye közzé a kapcsolódó Azure-erőforrások egy csoportját egyetlen központi telepítési művelettel. Többet is megtudhat velük kapcsolatban itt: [létrehozása és telepítése az Azure erőforráscsoport-sablonok a Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Előfeltételek
A Visual Studióban a Virtual Machine Scale Sets üzembe helyezésének első lépései, a következőkre lesz szüksége:

* A Visual Studio 2013-as vagy újabb
* Az Azure SDK 2.7-es, 2.8-as vagy 2.9

>[!NOTE]
>Ezek az utasítások feltételezik, hogy használja a Visual Studio [Azure SDK 2.8-as](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Projekt létrehozása
1. Hozzon létre egy új projektet a Visual Studióban kiválasztásával **fájl |} Új |} Projekt**.
   
    ![Új fájl][file_new]

2. A **Visual C# |} Felhőalapú**, válassza a **Azure Resource Manager** , hozzon létre egy projektet egy Azure Resource Manager-sablon üzembe helyezéséhez.
   
    ![Projekt létrehozása][create_project]

3. A sablonok listájában válassza ki a Linux vagy Windows virtuális gép méretezési beállítása sablon.
   
   ![Sablon kiválasztása][select_Template]

4. A projekt létrehozása után láthatja PowerShell telepítési parancsprogramjaival, az Azure Resource Manager-sablonok és alkalmazásparaméter-fájlt a virtuálisgép-méretezési.
   
    ![Megoldáskezelő][solution_explorer]

## <a name="customize-your-project"></a>A projekt testreszabása
Most már szerkesztheti a sablont testre szabni az alkalmazás igényeinek megfelelően, például a VM-bővítmény tulajdonságai hozzáadása vagy szerkesztése a terheléselosztási szabályok. Alapértelmezés szerint a virtuálisgép-méretezési csoport beállítása sablonok üzembe helyezéséhez a AzureDiagnostics bővítményt, ami lehetővé teszi az automatikus skálázási szabályok hozzáadásához egyszerűen vannak konfigurálva. Emellett központilag telepíti a terheléselosztó nyilvános IP-címmel, bejövő NAT-szabályok konfigurálva. 

A terheléselosztó lehetővé teszi, hogy az SSH (Linux) vagy RDP (Windows) a Virtuálisgép-példányokhoz csatlakozhat. Az előtér-porttartománya 50000-nél kezdődik. Ez azt jelenti, hogy ha a Linux rendszeren, ssh-n, 50000-es port, kapcsolódóak pedig az első a méretezési csoportban lévő virtuális gép 22-es portot. Kapcsolódás az 50001-es port irányítja a rendszer a második virtuális gép 22-es port és így tovább.

 Egy jó módszer a Visual Studio-sablonok szerkesztésekor, hogy a JSON-vázlat a paraméterek, a változók és az erőforrások rendszerezéséhez. A Visual Studio a séma ismeretében mutathat hibák a sablonban meg az üzembe helyezés előtt.

![JSON-Explorer][json_explorer]

## <a name="deploy-the-project"></a>A projekt telepítése
1. Telepítse az Azure Resource Manager-sablon a Virtual Machine Scale Set-erőforrás létrehozásához. Kattintson a jobb gombbal a projektcsomópontra, majd válassza **üzembe helyezés |} Új központi telepítés**.
   
    ![Sablon üzembe helyezése][5deploy_Template]
    
2. A "Üzembe helyezése a Resource Group" párbeszédpanelen válassza ki az előfizetését.
   
    ![Sablon üzembe helyezése][6deploy_Template]

3. Itt létrehozhat egy Azure-erőforráscsoportot, a sablon üzembe helyezéséhez.
   
    ![Új erőforráscsoport][new_resource]

4. Ezután kattintson **paraméterek szerkesztése** , adja meg a sablon átadott paramétereket. Adja meg a felhasználónevet és jelszót az operációs rendszer, amely a központi telepítés létrehozásához szükséges. Ha nem rendelkezik nástroje PowerShell Tools for Visual Studio telepítése, javasoljuk, hogy ellenőrizze **jelszavak mentése egyszerű** elkerülése érdekében egy rejtett PowerShell-parancssort, vagy használjon [keyvault támogatási](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Paraméterek szerkesztése][edit_parameters]

5. Most kattintson **telepítés**. A **kimeneti** ablakban jelennek meg az üzembehelyezési folyamatot. Vegye figyelembe, hogy a művelet végrehajtása a **üzembe helyezés – AzureResourceGroup.ps1** parancsfájlt.
   
   ![Kimeneti ablak][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>A virtuális gép méretezési felfedezése
Miután befejeződött a telepítés, megtekintheti az új virtuálisgép-méretezési a Visual Studio **Cloud Explorer** (a lista frissítéséhez). Cloud Explorer lehetővé teszi alkalmazások fejlesztése során a Visual Studio Azure-erőforrások kezelését. Megtekintheti a Virtual Machine Scale Set a a [az Azure portal](https://portal.azure.com) és [Azure erőforrás-kezelő](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 A portál biztosítja a legjobb módszer az Azure-infrastruktúra egy webböngészővel rendelkező vizuálisan felügyelheti Azure erőforrás-kezelő vizsgálata és az Azure-erőforrások hibakeresése egyszerű megoldást kínál a ablak így be az "instance-view", és a PowerShell-parancsokat is megjelenítése az erőforrások helyzet.

## <a name="next-steps"></a>További lépések
Miután sikeresen telepítette a Visual Studión keresztül a Virtual Machine Scale Sets, további szabhatja testre a projekthez, hogy az alkalmazás követelményeinek. Például adja hozzá az automatikus méretezés beállítása egy **Insights** erőforrás, infrastruktúra ad hozzá a sablont (például önálló virtuális gépeket), illetve az egyéni szkriptek bővítményét használó alkalmazások telepítését. Jó példa sablonok megtalálható a [Azure gyorsindítási sablonok](https://github.com/Azure/azure-quickstart-templates) GitHub-adattárból (Keresés az "vmss-hez").

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
