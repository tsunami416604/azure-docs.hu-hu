---
title: Vegyen fel egy Azure verem Piactéri elemet egy helyi forrásból |} Microsoft Docs
description: Ismerteti, hogyan lehet a helyi operációs rendszer lemezképének hozzáadása a verem Azure piactéren.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: a093e60718881b2fe9ca70df7596e8963dc55d9f
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808043"
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Oktatóanyag: Azure verem piactér elem hozzáadása a helyi forrásból

Kezelőként Azure verem elsőként kell tennie a felhasználók telepíteni a virtuális gép (VM) a Virtuálisgép-lemezkép hozzáadása a verem Azure piactér. Alapértelmezés szerint nem közzé van téve a verem Azure piactér, de feltöltheti a virtuális gép ISO-lemezképeket, hogy elérhetővé tétele a felhasználók számára. Használja ezt a beállítást, ha telepített Azure verem leválasztott esetén, vagy olyan forgatókönyvekben, a korlátozott kapcsolatban.

Ebben az oktatóanyagban a Windows Server 2016 Virtuálisgép-lemezkép letöltése a a Windows Server értékelések lapon, és töltse fel a verem Azure piactéren.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Windows Server 2016 Virtuálisgép-lemezkép hozzáadása
> * Ellenőrizze, hogy a Virtuálisgép-lemezkép érhető el 
> * A Virtuálisgép-lemezkép tesztelése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Telepítse a [Azure verem-kompatibilis Azure PowerShell-modulok](asdk-post-deploy.md#install-azure-stack-powershell)
- Töltse le a [Azure verem eszközök](asdk-post-deploy.md#download-the-azure-stack-tools)
- Töltse le a [Windows Server 2106 virtuális gép ISO-lemezképet](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) a Windows Server értékelések lapról

## <a name="add-a-windows-server-vm-image"></a>A Windows Server Virtuálisgép-lemezkép hozzáadása
A Windows Server 2016 kép adja hozzá a korábban letöltött ISO-lemezképet, PowerShell-lel közzéteheti a verem Azure piactéren. 

Használja ezt a beállítást, ha telepített Azure verem leválasztott esetén, vagy olyan forgatókönyvekben, a korlátozott kapcsolatban.

1. [Telepítse a PowerShell Azure verem](../azure-stack-powershell-install.md).

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. Ha használ **Active Directory összevonási szolgáltatások**, használja a következő parancsmagot:

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Jelentkezzen be Azure verem operátor. Útmutatásért lásd: [jelentkezzen be Azure verem kezelőként](../azure-stack-powershell-configure-admin.md).

   ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

4. A Windows Server 2016-lemezkép hozzáadása a verem Azure piactéren.

    A **Add-AzsPlatformimage** parancsmag használatával adja meg a lemezkép hivatkoznak a Virtuálisgép-lemezkép az Azure Resource Manager-sablonok segítségével értékeket adja meg.
    
    Az értékek a következők:
    
  - **publisher**  
    Például:`Microsoft`  
    A közzétevő neve szegmensében a Virtuálisgép-lemezkép felhasználók használni, amikor a lemezképet. Példa: **Microsoft**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **offer**  
    Például:`WindowsServer`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép ajánlat neve szegmense. Példa: **WindowsServer**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **sku**  
    Például:`Datacenter2016`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép SKU neve szegmense. Példa: **Datacenter2016**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **Verzió**  
    Például:`1.0.0`  
    A felhasználók használni, amikor azok a Virtuálisgép-lemezkép központi telepítése Virtuálisgép-lemezkép verziója. Ebben a formátumban van  *\#.\#. \#*. Példa: **1.0.0**. Nem tartalmaz szóközt vagy speciális karaktereket ebben a mezőben.  
  - **osType**  
    Például:`Windows`  
    A kép osType kell lennie, vagy **Windows** vagy **Linux**. Cserélje le *fully_qualified_path_to_ISO* az elérési útját a Windows Server 2016 ISO letöltött. 
  - **OSUri**  
    Például:`https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    A blobtárolók URI azonosítójához használandó is megadhat az egy `osDisk`. Abban az esetben adhatja a letöltött lemezkép tárolási helyét.

    További információkért lásd: a PowerShell-referencia az a [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) parancsmag.

    PowerShell nyisson meg egy rendszergazdai jogú parancssorba, majd futtassa:

      ````PowerShell  
        Add-AzsPlatformimage -publisher "Microsoft" -offer "WindowsServer" -sku "Datacenter2016" -version "1.0.0” -OSType "Windows" -OSUri "<fully_qualified_path_to_ISO>"
      ````  

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
> * A Windows Server 2016 Virtuálisgép-lemezkép hozzáadása
> * Ellenőrizze, hogy a Virtuálisgép-lemezkép érhető el 
> * A Virtuálisgép-lemezkép tesztelése

A következő oktatóanyag áttekintésével megismerheti, hogyan Azure verem ajánlat és terv létrehozása továbblépés.

> [!div class="nextstepaction"]
> [Azure verem IaaS szolgáltatást kínál](asdk-offer-services.md)
