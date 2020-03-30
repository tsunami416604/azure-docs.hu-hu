---
title: Virtuálisgép-méretezési készlet telepítése a Visual Studio használatával
description: Virtuálisgép-méretezési készletek telepítése a Visual Studio és az Erőforrás-kezelő sablon használatával
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066964"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Virtuálisgép-méretezési készlet létrehozása a Visual Studio segítségével

Ez a cikk bemutatja, hogyan telepíthet egy Azure virtuálisgép-méretezési készletet a Visual Studio erőforráscsoport központi telepítésével.

[Az Azure virtual machine scale sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) egy Azure Compute erőforrás üzembe helyezéséhez és kezeléséhez hasonló virtuális gépek automatikus skálázás és terheléselosztás gyűjteménye. Virtuálisgép-méretezési készleteket az [Azure Resource Manager-sablonokkal](https://github.com/Azure/azure-quickstart-templates)hozhat létre és telepíthet. Az Azure Resource Manager-sablonok telepíthetők az Azure CLI, a PowerShell, a REST és közvetlenül a Visual Studio használatával. A Visual Studio példasablonokat biztosít, amelyeket egy Azure Resource Group telepítési projekt részeként telepíthet.

Az Azure Resource Group központi telepítések egy módja annak, hogy csoportosítsa és tegye közzé a kapcsolódó Azure-erőforrások egy készletét egyetlen központi telepítési műveletben. További információt az [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióban című témakörben talál.](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

## <a name="prerequisites"></a>Előfeltételek

A virtuálisgép-méretezési készletek Visual Studio-ban történő üzembe helyezésének megkezdéséhez a következő előfeltételekre van szükség:

* Visual Studio 2013-as vagy újabb verzió
* Azure SDK 2.7, 2.8 vagy 2.9

>[!NOTE]
>Ez a cikk a Visual Studio 2019-et használja az [Azure SDK 2.8-as sal.](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

## <a name="create-a-project"></a>Projekt létrehozása<a name="creating-a-project"></a> 

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása**csoportban válassza az Azure Resource **Group** for C# lehetőséget, majd válassza a **Tovább**gombot.

1. Az **Új projekt konfigurálása**csoportban adjon meg egy nevet, és válassza a **Létrehozás lehetőséget.**

    ![A projekt elnevezése és létrehozása](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. A sablonok listájában válassza a **Windows virtuálisgép-méretezési készlet** vagy a Linux **virtuálisgép-méretezési készlet sablont.** Válassza **az OK gombot.**

   ![Virtuálisgép-sablon kiválasztása](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

A projekt létrehozása után a **Solution Explorer** tartalmaz egy PowerShell-telepítési parancsfájlt, egy Azure Resource Manager-sablont és egy paraméterfájlt a virtuálisgép-méretezési készlethez.

## <a name="customize-your-project"></a>A projekt testreszabása

Most szerkesztheti a sablont, hogy testre szabhassa az alkalmazás igényeinek megfelelően. Hozzáadhat virtuálisgép-bővítmény tulajdonságokat, vagy szerkesztheti a terheléselosztási szabályokat. Alapértelmezés szerint a virtuális gép méretezési készlet sablonjai az **AzureDiagnostics** bővítmény üzembe helyezésére vannak konfigurálva, ami megkönnyíti az automatikus skálázási szabályok hozzáadását. A sablonok egy nyilvános IP-címmel rendelkező, bejövő NAT-szabályokkal konfigurált terheléselosztót is üzembe helyeznek.

A terheléselosztó lehetővé teszi, hogy sSH (Linux) vagy RDP (Windows) segítségével csatlakozzon a virtuális gép példányaihoz. Az előtér-port tartománya 50000-től kezdődik. Linux esetén, ha az SSH 50000-es portra, a terheléselosztás a méretezési készlet első virtuális gépének 22-es portjához vezet. Az 50001-es porthoz való csatlakozás a második virtuális gép 22-es portjára van irányítva, és így tovább.

 A Visual Studio segítségével szerkesztheti a sablonokat, ha a **JSON Outline**programot használja. A paramétereket, a változókat és az erőforrásokat rendszerezheti. A séma ismeretében a Visual Studio a telepítés előtt rámutathat a sablon hibáira.

![JSON felfedező](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>A projekt telepítése

Az Azure Resource Manager-sablon üzembe helyezése a virtuálisgép-méretezési készlet erőforrás létrehozásához:

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre, és válassza az**Új** **telepítése parancsot.** > 

    ![A projekt üzembe helyezése](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. A **Deploy to Resource Group**alkalmazásban válassza ki, hogy melyik előfizetést használja, és válassza ki az erőforráscsoportot. Szükség esetén létrehozhat erőforráscsoportot.

1. Ezután válassza **a Paraméterek szerkesztése** lehetőséget a sablonnak átadott paraméterek megadásához.

   ![Előfizetés és erőforráscsoport megadása](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Adja meg az operációs rendszer felhasználónevét és jelszavát. Ezek az értékek szükségesek a központi telepítés létrehozásához. Ha nincs telepítve a PowerShell-eszközök a Visual Studio-hoz, válassza a **Jelszavak mentése lehetőséget** a rejtett PowerShell-parancssor elkerülése érdekében, vagy használja a Key Vault [támogatását.](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/) A folytatáshoz válassza a **Mentés** gombot.

    ![Telepítési paraméterek szerkesztése](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. A **Központi telepítés az erőforráscsoportba**csoportban válassza a **Deploy**lehetőséget. A művelet futtatja a **Deploy-AzureResourceGroup.ps1** parancsfájlt. A **Kimenet** ablak a központi telepítés folyamatát mutatja.

   ![A kimenet eredményeket jelenít meg](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>A virtuális gép méretezési készletének felfedezése<a name="exploring-your-virtual-machine-scale-set"></a>

Válassza a**Felhőkezelő** **megtekintése** > lehetőséget az új virtuálisgép-méretezési készlet megtekintéséhez. Szükség esetén használja **az Összes frissítése**parancsot.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**A Cloud Explorer** lehetővé teszi az Azure-erőforrások kezelését a Visual Studióban, miközben alkalmazásokat fejleszt. A virtuális gép méretezési készletét az [Azure Portalon](https://portal.azure.com) és az [Azure Resource Explorerben](https://resources.azure.com/)is megtekintheti.

 A portál a legjobb módja az Azure-infrastruktúra webböngészővel való kezeléséhez. Az Azure Resource Explorer segítségével egyszerűen feltárhatja és debugelheti az Azure-erőforrásokat. Az Azure Resource Explorer a példánynézetet kínálja, és a nézett erőforrásokHoz powershell-parancsokat is megjelenít.

## <a name="next-steps"></a>További lépések

Miután sikeresen telepítette a virtuális gép méretezési készleteit a Visual Studióban, tovább testreszabhatja a projektet az alkalmazás követelményeinek megfelelően. Konfigurálja például az automatikus skálázást egy **Insights-erőforrás** hozzáadásával. Hozzáadhat infrastruktúrát a sablonhoz, például önálló virtuális gépeket, vagy alkalmazásokat telepíthet az egyéni parancsfájlbővítmény használatával. Jó példa sablonok találhatók az [Azure Quickstart Sablonok](https://github.com/Azure/azure-quickstart-templates) GitHub-tárházban. Keressen a `vmss` kifejezésre.
