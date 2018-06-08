---
title: Telepítse újra az Azure verem szoftverfejlesztői készlet (ASDK) |} Microsoft Docs
description: Ebből a cikkből megismerheti, hogyan újra kell telepítenie a ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 91b8a936215e906e6e5b7e6a4fcd0dc88bef6009
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850320"
---
# <a name="redeploy-the-asdk"></a>Telepítse újra a ASDK
Ebből a cikkből megismerheti, hogyan újratelepíteni az Azure verem Development Kit (ASDK) nem éles környezetben. A ASDK frissítése nem támogatott, mert szükség teljesen újratelepítése áthelyezése egy újabb verzióra. A ASDK szeretné teljesen elölről bármikor központilag is telepítheti.

> [!IMPORTANT]
> A ASDK egy új verzióra való frissítés nem támogatott. Akkor kell újratelepíteni az development kit állomáson ASDK minden alkalommal, amikor egy újabb verziója Azure verem ki kell számítani.

## <a name="remove-azure-registration"></a>Távolítsa el az Azure regisztrációs 
Ha a ASDK telepítést már korábban regisztrált, az Azure-ral, ismételt üzembe helyezése a ASDK előtt távolítsa el a regisztrációs erőforrás. Regisztrálja újra a ASDK piactér szindikálási engedélyezése a ASDK újratelepítésekor. Ha nem már korábban regisztrált a ASDK Azure-előfizetéséhez Ez a szakasz kihagyhatja.

A regisztrációs erőforrás eltávolításához használja a **Remove-AzsRegistration** parancsmag Azure verem regisztrációját. Ezt követően a **Remove-AzureRMRsourceGroup** parancsmag a verem Azure erőforráscsoport törlése az Azure-előfizetésből:

1. Nyissa meg a PowerShell-konzolban a számítógépen, amely hozzáfér a kiemelt végpont rendszergazdaként. A ASDK esetén, amelyek a development kit gazdaszámítógépen.

2. Futtassa a következő PowerShell-parancsokat regisztrációjának törlése az ASDK telepítése és törlése a **azurestack** erőforráscsoportot az Azure-előfizetésből:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Add-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Jelentkezzen be az Azure-előfizetéssel, mind a helyi ASDK telepítési futtatott parancsfájl kéri.
4. A parancsprogram befejezését követően megjelenik az alábbi példák hasonló üzenetek jelenhetnek meg:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Az Azure verem sikeresen kell az Azure-előfizetés regisztrációját. Emellett a azurestack erőforráscsoport, jön létre, amikor Azure-ban regisztrált a ASDK is törölni kell.

## <a name="deploy-the-asdk"></a>A ASDK telepítése
Telepítse újra az Azure-vermet, el kell indítania keresztül teljesen az alább ismertetett. A lépések végrehajtására-e meg az Azure-vermet (asdk-installer.ps1) parancsfájl telepítéséhez használt a ASDK függően eltérnek.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Telepítse újra a telepítő parancsfájllal ASDK
1. A ASDK számítógépen nyisson meg egy emelt szintű PowerShell-konzolt, és keresse meg a asdk-installer.ps1 parancsfájl a **AzureStack_Installer** directory rendszert nem tartalmazó meghajtón található. Futtassa a parancsfájlt, és kattintson a **újraindítás**.

   ![A asdk-installer.ps1 parancsprogrammal](media/asdk-redeploy/1.png)

2. Válassza ki az operációs rendszer (nem **Azure verem**), majd **következő**.

   ![Indítsa újra a számítógépet a gazda operációs rendszer](media/asdk-redeploy/2.png)

3. Miután a development kit gazdagép újraindítja az operációs rendszer, jelentkezzen be helyi rendszergazdaként. Keresse meg és törölje a **C:\CloudBuilder.vhdx** a korábbi központi telepítés részeként használt fájlt. 

4. Ismételje meg a megtette az első lépéseket [központi telepítése a ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Telepítse újra a ASDK a telepítő használata nélkül
Ha nem használja a asdk-installer.ps1 parancsfájl a ASDK telepítéséhez, manuálisan kell konfigurálnia a development kit gazdaszámítógép ismételt üzembe helyezése a ASDK előtt.

1. Indítsa el a rendszer-konfigurációs segédprogram futtatásával **msconfig.exe** ASDK számítógépen. Az a **rendszerindító** lapra, válassza ki a gazdagép operációs rendszere (nem Azure-verem), a **alapértelmezett**, és kattintson a **OK**. Kattintson a **indítsa újra a** megjelenésekor.

      ![A rendszerindítási konfigurációs beállítása](media/asdk-redeploy/4.png)

2. Miután a development kit gazdagép újraindítja az operációs rendszer, jelentkezzen be helyi rendszergazdaként a development kit gazdagépnek. Keresse meg és törölje a **C:\CloudBuilder.vhdx** a korábbi központi telepítés részeként használt fájlt. 

3. Ismételje meg a megtette az első lépéseket [központi telepítése a PowerShell használatával ASDK](asdk-deploy-powershell.md).


## <a name="next-steps"></a>További lépések
[ASDK telepítés utáni konfigurációs feladatok](asdk-post-deploy.md)




