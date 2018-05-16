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
ms.openlocfilehash: 325ef42d72970f4e0962a9b1a81b78bbd39585d4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
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

1. Importálja az Azure-verem `Connect` és `ComputeAdmin` PowerShell-modulok az Azure-verem tools könyvtárban szerepel a következő parancsokkal:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Futtassa a ASDK fogadó számítógép attól függően, hogy az Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) használatával telepített Azure verem környezetét az alábbi parancsfájlok egyikét:

  - A parancsok **az Azure AD-telepítések**: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - A parancsok **AD FS-telepítések**:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Add-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. A Windows Server 2016-lemezkép hozzáadása a verem Azure piactéren. (Csere *fully_qualified_path_to_ISO* az elérési útját a Windows Server 2016 ISO letöltött.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

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
