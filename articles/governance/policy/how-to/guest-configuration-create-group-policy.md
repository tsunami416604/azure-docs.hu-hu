---
title: Vendég-konfigurációs házirend definícióinak létrehozása a Windows Csoportházirend alapkonfigurációból
description: Megtudhatja, hogyan alakíthatja át Csoportházirend a Windows Server 2019 biztonsági alaptervből egy házirend-definícióba.
ms.date: 06/05/2020
ms.topic: how-to
ms.openlocfilehash: bbb634ed55acf8aa994045fbef6569fae031c841
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86080669"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Vendég-konfigurációs házirend definícióinak létrehozása a Windows Csoportházirend alapkonfigurációból

Az egyéni házirend-definíciók létrehozása előtt érdemes beolvasni a koncepcionális áttekintési információkat [Azure Policy vendég konfigurációjában](../concepts/guest-configuration.md). A Linux rendszerhez készült egyéni vendég-konfigurációs házirend-definíciók létrehozásával kapcsolatos további információkért lásd: [vendég-konfigurációs szabályzatok létrehozása Linux rendszerhez](./guest-configuration-create-linux.md). További információ a Windows rendszerhez készült egyéni konfigurációs házirend-definíciók létrehozásáról: [vendég konfigurációs szabályzatok létrehozása Windows rendszerhez](./guest-configuration-create.md). 

A Windows naplózásakor a vendég konfigurációja a [kívánt állapot-konfigurációs](/powershell/scripting/dsc/overview/overview) (DSC) erőforrás-modult használja a konfigurációs fájl létrehozásához. A DSC-konfiguráció azt a feltételt határozza meg, amelyet a gépen be kell állítani. Ha a konfiguráció kiértékelése **nem megfelelő**, akkor a rendszer kiváltja a házirend hatására *auditIfNotExists* . [Azure Policy vendég konfiguráció](../concepts/guest-configuration.md) csak a gépeken belüli beállításokat naplózza.

> [!IMPORTANT]
> A vendég-konfigurációval rendelkező egyéni házirend-definíciók előzetes verziójú funkciók.
>
> Az Azure Virtual Machines szolgáltatásban végzett naplózáshoz a vendég konfigurációs bővítmény szükséges.
> Ha a bővítményt az összes Windows rendszerű gépen szeretné üzembe helyezni, rendelje hozzá a következő szabályzat-definíciókat:
>   - [Telepítse az előfeltételeket, hogy engedélyezze a vendég-konfigurációs házirendet a Windows rendszerű virtuális gépeken.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

A DSC-Közösség közzétette a [BaselineManagement modult](https://github.com/microsoft/BaselineManagement) az exportált csoportházirend-sablonok DSC formátumra való konvertálásához. A GuestConfiguration parancsmaggal együtt a BaselineManagement modul Azure Policy vendég konfigurációs csomagot hoz létre a Windows rendszerhez Csoportházirend tartalomból. A BaselineManagement modul használatáról további információt a rövid útmutató [: csoportházirend konvertálása a DSC-be](/powershell/scripting/dsc/quickstarts/gpo-quickstart)című cikkben talál. 

Ebből az útmutatóból megtudhatja, hogyan hozhat létre Azure Policy vendég konfigurációs csomagot egy Csoportházirend objektumból (GPO). Míg az útmutató a Windows Server 2019 biztonsági alapkonfigurációjának átalakítását ismerteti, ugyanez a folyamat alkalmazható más csoportházirend-objektumokra is.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Töltse le a Windows Server 2019 biztonsági alaptervét, és telepítse a kapcsolódó PowerShell-modulokat

A **DSC**, a **GuestConfiguration**, az alapkonfiguráció **kezelése**és a kapcsolódó Azure-modulok telepítése a PowerShellben:

1. A PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Hozzon létre egy könyvtárat, és töltse le a Windows Server 2019 biztonsági alaptervet a Windows biztonsági megfelelőségi eszközkészletből.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Oldja fel és bontsa ki a letöltött kiszolgáló 2019 alaptervét.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Ellenőrizze a kiszolgáló 2019 alapkonfigurációjának tartalmát **MapGuidsToGpoNames.ps1**használatával.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Konvertálás Csoportházirendról Azure Policy vendég konfigurációra

Ezután a letöltött Server 2019 alapkonfigurációt egy vendég konfigurációs csomagba konvertáljuk a vendég konfigurációja és az alapkonfiguráció felügyeleti modulok használatával. 

1. Alakítsa át a Csoportházirendt a kívánt állapotra az alapkonfiguráció felügyeleti moduljának használatával.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Az átalakított parancsfájlok átnevezése, formázása és futtatása a házirend-csomag létrehozása előtt.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Hozzon létre egy Azure Policy vendég konfigurációs tartalomkezelő csomagot.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Azure Policy vendég konfigurációjának létrehozása

A következő lépés a fájl közzététele a blob Storage-ban. 

1. Az alábbi szkript a feladat automatizálásához használható függvényt tartalmaz. Megjegyzés: a függvényben használt parancsokhoz `publish` szükség van a `Az.Storage` modulra.

   ```azurepowershell-interactive
    function Publish-Configuration {
        param(
        [Parameter(Mandatory=$true)]
        $resourceGroup,
        [Parameter(Mandatory=$true)]
        $storageAccountName,
        [Parameter(Mandatory=$true)]
        $storageContainerName,
        [Parameter(Mandatory=$true)]
        $filePath,
        [Parameter(Mandatory=$true)]
        $blobName
        )

        # Get Storage Context
        $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
            -Name $storageAccountName | `
            ForEach-Object { $_.Context }

        # Upload file
        $Blob = Set-AzStorageBlobContent -Context $Context `
            -Container $storageContainerName `
            -File $filePath `
            -Blob $blobName `
            -Force

        # Get url with SAS token
        $StartTime = (Get-Date)
        $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
        $SAS = New-AzStorageBlobSASToken -Context $Context `
            -Container $storageContainerName `
            -Blob $blobName `
            -StartTime $StartTime `
            -ExpiryTime $ExpiryTime `
            -Permission rl `
            -FullUri

        # Output
        return $SAS
    }
   ```

1. Hozzon létre paramétereket az egyedi erőforráscsoport, a Storage-fiók és a tároló definiálásához. 
   
   ```azurepowershell-interactive
    # Replace the $resourceGroup, $storageAccount, and $storageContainer values below.
    $resourceGroup = 'rfc_customguestconfig'
    $storageAccount = 'guestconfiguration'
    $storageContainer = 'content'
    $path = 'c:\git\policyfiles\Server2019Baseline\Server2019Baseline.zip'
    $blob = 'Server2019Baseline.zip' 
    ```

1. A közzétételi függvénnyel a hozzárendelt paraméterekkel közzéteheti a vendég konfigurációs csomagot a nyilvános blob Storage szolgáltatásban.


   ```azurepowershell-interactive
   $PublishConfigurationSplat = @{
       resourceGroup = $resourceGroup
       storageAccountName = $storageAccount
       storageContainerName = $storageContainer
       filePath = $path
       blobName = $blob
       FullUri = $true
   }
   $uri = Publish-Configuration @PublishConfigurationSplat
    ```
1. Miután létrehozta és feltöltötte a vendég konfigurációhoz tartozó egyéni házirend-csomagot, hozza létre a vendég-konfigurációs házirend definícióját. A `New-GuestConfigurationPolicy` parancsmag használatával hozza létre a vendég konfigurációját.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Tegye közzé a szabályzat-definíciókat a `Publish-GuestConfigurationPolicy` parancsmag használatával. A parancsmag csak a **path** paraméterrel rendelkezik, amely a által létrehozott JSON-fájlok helyére mutat `New-GuestConfigurationPolicy` . A közzétételi parancs futtatásához hozzá kell férnie a szabályzat-definíciók létrehozásához az Azure-ban. A konkrét engedélyezési követelmények dokumentálva vannak a [Azure Policy áttekintés](../overview.md#getting-started) oldalon. A legjobb beépített szerepkör az erőforrás- **házirend közreműködője**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Vendég-konfigurációs házirend definíciójának társítása

Az Azure-ban létrehozott szabályzattal az utolsó lépés a kezdeményezés kiosztása. Lásd: a kezdeményezés társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

> [!IMPORTANT]
> A _AuditIfNotExists_ és a _DeployIfNotExists_ szabályzatot egyesítő kezdeményezéssel **mindig** hozzá kell rendelni a vendég-konfigurációs házirend definícióit. Ha csak a _AuditIfNotExists_ szabályzat van hozzárendelve, az előfeltételek nincsenek telepítve, és a házirend mindig azt mutatja, hogy a "0" kiszolgálók megfelelőek.

Egy szabályzat-definíció _DeployIfNotExists_ -effektussal való hozzárendeléséhez további hozzáférési szint szükséges. A legalacsonyabb jogosultság megadásához létrehozhat egy egyéni szerepkör-definíciót, amely kibővíti az **erőforrás-házirend közreműködőjét**. Az alábbi példa létrehoz egy erőforrás- **házirend közreműködője** nevű szerepkört a _Microsoft. Authorization/roleAssignments/Write_további engedélyeivel.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>További lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](get-compliance-data.md).
