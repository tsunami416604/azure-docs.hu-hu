---
title: "Vegyen fel egy Azure verem Piactéri elemet az Azure-ból |} Microsoft Docs"
description: "Ismerteti, hogyan lehet egy Azure-alapú Windows Server virtuálisgép-lemezkép hozzáadása a verem Azure piactéren."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 25f179f825526e20377c0e94ccb38a788cadd898
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-azure"></a>Oktatóanyag: Azure verem piactér elem hozzáadása az Azure-ból

Kezelőként Azure verem elsőként kell tennie a felhasználók telepíteni a virtuális gép (VM) a Virtuálisgép-lemezkép hozzáadása a verem Azure piactér. Alapértelmezés szerint a verem Azure piactér semmi van közzétéve, de letöltheti a tételekhez [az Azure piactéren elemek válogatott listája](.\.\azure-stack-marketplace-azure-items.md) amelyeket tesztelt Azure veremben futtatásához. Használja ezt a beállítást, ha olyan csatlakoztatott forgatókönyvekben működik, és regisztrálta a Azure verem példányát az Azure-ral.

Ebben az oktatóanyagban ad hozzá a Windows Server 2016 Virtuálisgép-lemezkép az Azure piactér a verem Azure piactéren.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Adja hozzá a Windows Server virtuális gép Azure csomagjának Piactéri elemet
> * Ellenőrizze, hogy a Virtuálisgép-lemezkép érhető el 
> * A Virtuálisgép-lemezkép tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Azure verem-kompatibilis Azure PowerShell-modulok](asdk-post-deploy.md#install-azure-stack-powershell)
- Töltse le a [Azure verem eszközök](asdk-post-deploy.md#download-the-azure-stack-tools)
- [Regisztrálja a ASDK](asdk-register.md) Azure-előfizetéséhez

## <a name="add-a-windows-server-vm-image"></a>A Windows Server Virtuálisgép-lemezkép hozzáadása
A Windows Server 2016-lemezkép adhat a verem Azure piactér, ha a lemezkép letöltése a Azure piactérről. Használja ezt a beállítást, ha olyan csatlakoztatott forgatókönyvekben működik, és már rendelkezik [regisztrálva az Azure-verem példányának](asdk-register.md) az Azure-ral.

1. Jelentkezzen be a [ASDK felügyeleti portál](https://adminportal.local.azurestack.external).

2. Válassza ki **további szolgáltatások** > **piactér felügyeleti** > **hozzáadása az Azure-ból**. 

   ![Adja hozzá az Azure-ból](media/asdk-marketplace-item/azs-marketplace.png)

3. Található, vagy keresse meg a **Windows Server 2016 Datacenter** kép.

4. Válassza ki a **Windows Server 2016 Datacenter** rendszerképet, majd válassza ki **letöltése**.

   ![Töltse le a lemezképet az Azure-ból](media/asdk-marketplace-item/azure-marketplace-ws2016.png)


> [!NOTE]
> A Virtuálisgép-lemezkép letöltése, és tegye közzé a verem Azure piactér körülbelül egy óra vesz igénybe. 

A letöltés befejezése után, a kép lesz közzétéve a rendelkezésre álló **piactér felügyeleti**. A kép is érhető el a **számítási** új virtuális gépek létrehozásához.

## <a name="verify-the-marketplace-item-is-available"></a>Ellenőrizze, elérhető a Piactéri elemet
Ezen lépések segítségével győződjön meg arról, hogy az új Virtuálisgép-lemezkép érhető el a verem Azure piactéren.

1. Jelentkezzen be a [ASDK felügyeleti portál](https://adminportal.local.azurestack.external).

2. Válassza ki **további szolgáltatások** > **piactér felügyeleti**. 

3. Győződjön meg arról, hogy sikeresen hozzáadta a Windows Server 2016 Datacenter Virtuálisgép-lemezkép.

   ![Az Azure-ból letöltött kép](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>A Virtuálisgép-lemezkép tesztelése
Egy Azure verem operátort, használhatja a [adminisztrációs portálhoz](https://adminportal.local.azurestack.external) létrehozni a teszt ellenőrzése a kép VM elérhetővé tett sikeresen. 

1. Jelentkezzen be a [ASDK felügyeleti portál](https://adminportal.local.azurestack.external).

2. Kattintson a **új** > **számítási** > **Windows Server 2016 Datacenter** > **létrehozása**.  
 ![Hozzon létre virtuális Gépet Piactéri lemezképből](media/asdk-marketplace-item/new-compute.png)

3. Az a **alapjai** panelen adja meg a következő adatokat, és kattintson a **OK**:
  - **Név**: 1-vm-teszt
  - **Felhasználónév**: AdminTestUser
  - **Jelszó**: AzS-TestVM01
  - **Előfizetés**: fogadja el az alapértelmezett szolgáltató előfizetés
  - **Erőforráscsoport**: teszt-vm-rg
  - **Hely**: helyi

4. Az a **méret kiválasztása** panelen kattintson **A1 szabványos**, és kattintson a **kiválasztása**.  

5. Az a **beállítások** panelen fogadja el az alapértelmezett beállításokat, majd kattintson **OK**

6. Az **Összegzés** panelen kattintson az **OK** gombra a virtuális gép létrehozásához.  
> [!NOTE]
> A virtuális gépek telepítése során néhány percet is igénybe vehet.

7. Az új virtuális gép megtekintéséhez kattintson **virtuális gépek**, majd keresse meg a **-vm – 1. teszt** , majd kattintson annak nevére.
    ![Első vizsgálat Virtuálisgép-lemezkép jelenik meg](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Miután sikeresen bejelentkezett a virtuális Gépet, és ellenőrizte, hogy megfelelően működik-e a lemezképet, a teszt csoport törölni kell. Ez szabadít fel a korlátozott egycsomópontos ASDK telepítések számára elérhető erőforrások.

Már nincs szükség, ha törli az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások az alábbiak szerint:

1. Jelentkezzen be a [ASDK felügyeleti portál](https://adminportal.local.azurestack.external).
2. Kattintson a **erőforráscsoportok** > **teszt-vm-rg** > **erőforrás csoport törlése**.
3. Típus **teszt-vm-rg** , az erőforráscsoport neve majd **törlése**.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adja hozzá a Windows Server virtuális gép Azure csomagjának Piactéri elemet
> * Ellenőrizze, hogy a Virtuálisgép-lemezkép érhető el 
> * A Virtuálisgép-lemezkép tesztelése

A következő oktatóanyag áttekintésével megismerheti, hogyan Azure verem ajánlat és terv létrehozása továbblépés.

> [!div class="nextstepaction"]
> [Azure verem IaaS szolgáltatást kínál](asdk-offer-services.md)