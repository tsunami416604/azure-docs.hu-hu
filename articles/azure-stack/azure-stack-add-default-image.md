---
title: "Az alapértelmezett Virtuálisgép-lemezkép hozzáadása a verem Azure piactérről |} Microsoft Docs"
description: "Adja hozzá a Windows Server 2016 VM alapértelmezett verem Azure piactérről."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: e5e7ab55800eccea98cbd71ae32bdc611ee3e961
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>A Windows Server 2016 Virtuálisgép-lemezkép hozzáadása a verem Azure piactéren

Alapértelmezés szerint nem virtuálisgép-lemezképek érhetők el a veremben Azure piactéren. Egy Azure verem operátor lemezkép hozzá kell adnia a felhasználók számára hozzáférést a piactéren. Az Azure-verem piactéren a Windows Server 2016 lemezképet adhat hozzá az alábbi módszerek egyikének használatával:

* [Töltse le a lemezképet az Azure piactérről](#add-the-image-by-downloading-it-from-the-azure-marketplace). Használja ezt a beállítást, ha olyan csatlakoztatott forgatókönyvekben működik, és regisztrálta a Azure verem példányát az Azure-ral.

* [A lemezkép hozzáadása a PowerShell használatával](#add-the-image-by-using-powershell). Használja ezt a beállítást, ha telepített Azure verem leválasztott esetén, vagy olyan forgatókönyvekben, a korlátozott kapcsolatban.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>A lemezkép hozzáadása az Azure piactérről letöltésével

1. Központi telepítése az Azure-vermet, és jelentkezzen be a Azure verem szoftverfejlesztői készlet.

2. Válassza ki **további szolgáltatások** > **piactér felügyeleti** > **hozzáadása az Azure-ból**. 

3. Található, vagy keresse meg a **Windows Server 2016 Datacenter – Eval** lemezképet, majd válassza ki **letöltése**.

   ![Töltse le a lemezképet az Azure-ból](media/azure-stack-add-default-image/download-image.png)

A letöltést, a kép érhető el a **piactér felügyeleti**. A kép is érhető el a **virtuális gépek**.

## <a name="add-the-image-by-using-powershell"></a>A lemezkép hozzáadása a PowerShell használatával

### <a name="prerequisites"></a>Előfeltételek 

Futtassa a következő előfeltételek teljesülését, vagy a [szoftverfejlesztői készlet](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) vagy egy Windows-alapú külső ügyfélszámítógépről, ha [VPN-en keresztül csatlakozó](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. Telepítés [Azure verem-kompatibilis Azure PowerShell-modulok](azure-stack-powershell-install.md).  

2. Töltse le a [az Azure veremnek megfelelő működéséhez szükséges eszközök](azure-stack-powershell-download.md).  

3. A Windows Server értékelések lapon [töltse le a Windows Server 2016 kiértékelése](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). Amikor a rendszer kéri, válassza ki a letöltés a ISO verziót. Jegyezze fel a letöltési helyre, a cikkben leírt lépéseket a használt elérési útját. A lépéshez internetkapcsolatra van szükség.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Adja hozzá a verem Azure piactéren
   
1. Az Azure-verem Connect és ComputeAdmin modul importálása a következő parancsokkal:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Jelentkezzen be a verem Azure környezetben. Futtassa az alábbi parancsfájlok, attól függően, hogy az Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) használatával telepített Azure verem környezetét. (Cserélje le az Azure AD `tenantName`, `GraphAudience` végpontot, és `ArmEndpoint` beállításazonosítókban környezet konfigurációjától.)  

   * **Az Azure Active Directory**. Használja a következő parancsmagot:

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
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

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Az Active Directory összevonási szolgáltatások**. Használja a következő parancsmagot:
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

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

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. Adja hozzá a Windows Server 2016 verem Azure piactérről. (Csere *fully_qualified_path_to_ISO* az elérési útját a Windows Server 2016 ISO letöltött.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Győződjön meg arról, hogy a Windows Server 2016 Virtuálisgép-lemezkép van-e a legújabb összesítő frissítéssel, tartalmazza a `IncludeLatestCU` paraméter futtatásakor a `New-AzsServer2016VMImage` parancsmag. Engedélyezett paramétereinek kapcsolatos információkat a `New-AzsServer2016VMImage` parancsmag, lásd: [paraméterek](#parameters). A kép közzététele az Azure-verem piactéren körülbelül egy óra vesz igénybe. 

## <a name="parameters"></a>Paraméterek

|Új AzsServer2016VMImage paraméterek|Kötelező?|Leírás|
|-----|-----|------|
|ISOPath|Igen|A Windows Server 2016 ISO letöltött teljes elérési útja.|
|Net35|Nem|A .NET 3.5 futásidejű telepít a Windows Server 2016-lemezképet. Alapértelmezés szerint ez az érték értéke **igaz**.|
|Verzió|Nem|Adja meg **Core**, **teljes**, vagy **mindkét** Windows Server 2016-lemezképeket. Alapértelmezés szerint ez az érték értéke **teljes**.|
|VHDSizeInMB|Nem|Beállítja a mérete (MB) a virtuális merevlemez lemezképet, fel kell venni a verem Azure környezetben. Alapértelmezés szerint a értéke 40 960 MB-ra.|
|CreateGalleryItem|Nem|Meghatározza, hogy a Piactéri elemet kell létrehozni a Windows Server 2016-lemezképet. Alapértelmezés szerint ez az érték értéke **igaz**.|
|location |Nem |Adja meg a helyet, amelyhez a Windows Server 2016 kép közzé kell tenni.|
|IncludeLatestCU|Nem|A legújabb Windows Server 2016-os összesítő frissítéssel vonatkozik az új virtuális Merevlemezt.|
|CUUri |Nem |A Windows Server 2016 beállítása összegző frissítés egy adott URI-ről futtatva. |
|CUPath |Nem |Beállítja a Windows Server 2016 összegző frissítés futtatásához helyi útvonalon. Ez a beállítás akkor hasznos, ha telepítette az Azure-verem példány leválasztott környezetben.|

## <a name="next-steps"></a>Következő lépések

[Virtuális gép kiépítése](azure-stack-provision-vm.md)
