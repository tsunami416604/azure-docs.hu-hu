---
title: Vendég-konfigurációs szabályzatok létrehozása Windows rendszerhez
description: Megtudhatja, hogyan hozhat létre Azure Policy vendég-konfigurációs házirendet a Windows rendszerhez.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: a75525b25945dd9548d7c293d5965cc67eb463dc
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509618"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Vendég-konfigurációs szabályzatok létrehozása Windows rendszerhez

Az egyéni szabályzatok létrehozása előtt érdemes beolvasni a koncepcionális áttekintési információkat az oldalon [Azure Policy vendég konfiguráció](../concepts/guest-configuration.md).
 
A Linux rendszerhez készült vendég-konfigurációs szabályzatok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Linux rendszerhez készült vendég-konfigurációs szabályzatok létrehozása](./guest-configuration-create-linux.md) című oldalt.

A Windows naplózásakor a vendég konfigurációja a [kívánt állapot-konfigurációs](/powershell/scripting/dsc/overview/overview) (DSC) erőforrás-modult használja a konfigurációs fájl létrehozásához. A DSC-konfiguráció azt a feltételt határozza meg, amelyet a gépen be kell állítani.
Ha a konfiguráció kiértékelése meghiúsul, a rendszer elindítja a **auditIfNotExists** , és a gép **nem megfelelőnek**minősül.

[Azure Policy vendég konfiguráció](../concepts/guest-configuration.md) csak a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások szervizelése még nem érhető el.

A következő műveletek végrehajtásával hozhatja létre saját konfigurációját egy Azure-beli vagy nem Azure-beli gép állapotának ellenőrzéséhez.

> [!IMPORTANT]
> A vendég-konfigurációval rendelkező egyéni házirendek előzetes verziójú funkciók.
>
> Az Azure Virtual Machines szolgáltatásban végzett naplózáshoz a vendég konfigurációs bővítmény szükséges.
> Ha a bővítményt az összes Windows rendszerű gépen szeretné üzembe helyezni, rendelje hozzá a következő szabályzat-definíciókat:
>   - [Telepítse az előfeltételeket, hogy engedélyezze a vendég-konfigurációs házirendet a Windows rendszerű virtuális gépeken.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

A vendég konfigurációs összetevő létrehozása, az összetevő automatizált tesztelése, a szabályzat-definíció létrehozása és a szabályzat közzététele teljes mértékben automatizálható a PowerShell vendég konfigurációs moduljának használatával. A modul Windows, macOS vagy Linux rendszerű gépen telepíthető, amely a PowerShell 6,2-as vagy újabb verzióját futtatja helyileg, vagy [Azure Cloud Shell](https://shell.azure.com)vagy a [Azure PowerShell Core Docker-rendszerkép](https://hub.docker.com/r/azuresdk/azure-powershell-core)használatával.

> [!NOTE]
> A konfigurációk fordítása még nem támogatott Linux rendszeren.

### <a name="base-requirements"></a>Alapszintű követelmények

Azok az operációs rendszerek, amelyeken telepítve van a modul:

- Linux
- macOS
- Windows

A vendég konfigurációs erőforrás-modulhoz a következő szoftverek szükségesek:

- PowerShell 6,2 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).
  - Csak az az modulok "az. accounts" és az "az. Resources" megadása szükséges.

### <a name="install-the-module"></a>A modul telepítése

A **GuestConfiguration** modul telepítése a PowerShellben:

1. A PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Annak ellenőrzése, hogy a modul importálása megtörtént-e:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Vendég konfigurációs összetevők és szabályzat Windows rendszerhez

A vendég konfigurációja a PowerShell kívánt állapotának konfigurációját használja nyelvi absztrakcióként a Windows rendszerbe való naplózással kapcsolatos íráshoz. Az ügynök betölti a PowerShell 6,2 önálló példányát, így a PowerShell DSC használata nem ütközik a Windows PowerShell 5,1-ben, és nincs szükség a PowerShell 6,2 vagy újabb verziójának előzetes telepítésére.

A DSC-fogalmak és a terminológia áttekintését lásd: a [POWERSHELL DSC áttekintése](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>A vendég konfigurációs moduljai eltérnek a Windows PowerShell DSC moduljaitól

Ha a vendég konfigurációja naplóz egy gépet:

1. Az ügynök először futtatja `Test-TargetResource` annak megállapítására, hogy a konfiguráció helyes állapotban van-e.
1. A függvény által visszaadott logikai érték határozza meg, hogy a vendég-hozzárendelés Azure Resource Manager állapotának megfelelőnek vagy nem megfelelőnek kell lennie.
1. A szolgáltató úgy `Get-TargetResource` fut, hogy az egyes beállítások aktuális állapotát adja vissza, így a részletek mind arról szólnak, hogy a gép miért nem megfelelő, és hogy a jelenlegi állapot megfelelő-e.

### <a name="get-targetresource-requirements"></a>A Get-TargetResource követelményei

A függvény `Get-TargetResource` speciális követelményekkel rendelkezik a Windows kívánt állapotának konfigurálásához nem szükséges vendég-konfigurációhoz.

- A visszaadott szórótábla tartalmaznia kell **egy nevű**tulajdonságot.
- A Reason tulajdonságnak tömbnek kell lennie.
- A tömb minden elemének olyan szórótábla kell lennie, amelynek **kóddal** és **kifejezéssel**ellátott kulcsokkal kell rendelkeznie.

A szolgáltatás az okok tulajdonságát arra használja, hogy egységesítse, hogyan jelennek meg az információk, amikor egy gép nem felel meg az előírásoknak. Az egyes elemek oka az lehet, hogy az erőforrás nem megfelelő. A tulajdonság egy tömb, mert egy erőforrás több okból nem felel meg a megfelelőségnek.

A szolgáltatás a tulajdonságok **kódját** és a **kifejezést** is elvárta. Egyéni erőforrás létrehozásakor állítsa be azt a szöveget (jellemzően StdOut), amelyet az erőforrás nem felel meg a **kifejezés**értékének. A **kód** meghatározott formázási követelményekkel rendelkezik, így a jelentéskészítés egyértelműen megjelenítheti az erőforrással kapcsolatos információkat a naplózás végrehajtásához. Ez a megoldás a vendég konfigurációját bővíthetővé teszi. Bármely parancs futtatható, ha a kimenet karakterlánc-értékként adható vissza a **kifejezés** tulajdonsághoz.

- **Code** (string) (karakterlánc): az erőforrás neve, ismétlődő, majd egy rövid név, amely nem tartalmazhat szóközt azonosítóként az ok miatt. Ez a három érték csak kettősponttal tagolható szóközök nélkül.
  - Példa erre`registry:registry:keynotpresent`
- **Kifejezés** (karakterlánc): ember által olvasható szöveg, amely elmagyarázza, hogy a beállítás miért nem megfelelő.
  - Példa erre`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Az okok tulajdonságot az erőforráshoz tartozó MOF-hez is hozzá kell adni beágyazott osztályként.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Konfigurációs követelmények

Az egyéni konfiguráció nevének mindenütt konzisztensnek kell lennie. A Content csomag. zip fájljának nevét, a MOF-fájlban található konfiguráció nevét, valamint a Resource Manager-sablon vendég-hozzárendelési nevét meg kell egyeznie.

### <a name="scaffolding-a-guest-configuration-project"></a>Vendég konfigurációs projekt állványzata

Azok a fejlesztők, akik szeretnék felgyorsítani az első lépéseket, és a mintakód használatával dolgozhatnak egy, a **Guest Configuration Project**nevű közösségi projektben. A projekt egy sablont telepít a [vakolat](https://github.com/powershell/plaster) PowerShell-modulhoz. Ezzel az eszközzel a projekt beépíthető, beleértve a munkakonfigurációt és a minta erőforrást, valamint a projekt ellenőrzéséhez szükséges, a tevékenység ellenőrzésére szolgáló feldolgozatlan [teszteket](https://github.com/pester/pester) . A sablon a Visual Studio Code feladatait is tartalmazza, amelyekkel automatizálható a vendég-konfigurációs csomag kiépítése és érvényesítése. További információ: GitHub Project [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

A konfigurációk általános használatáról további információt a [konfiguráció írása, fordítása és alkalmazása](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)című témakörben talál.

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Vendég konfigurációs összetevő várt tartalma

A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához. A csomag a következőkből áll:

- A lefordított DSC-konfiguráció MOF-ként
- Modulok mappa
  - GuestConfiguration modul
  - DscNativeResources modul
  - Windows A MOF által igényelt DSC-erőforrás-modulok

A PowerShell-parancsmagok segítséget nyújtanak a csomag létrehozásában.
Nincs szükség gyökér szintű mappára vagy a Version mappára.
A csomag formátumának. zip formátumúnak kell lennie.

### <a name="storing-guest-configuration-artifacts"></a>Vendég konfigurációs összetevők tárolása

A. zip-csomagot a felügyelt virtuális gépek által elérhető helyen kell tárolni.
Ilyenek például a GitHub-adattárak, az Azure-Tárházak vagy az Azure Storage. Ha nem szeretné, hogy a csomag nyilvános legyen, az URL-címben egy [sas-tokent](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) is hozzáadhat.
A magánhálózati számítógépekhez [szolgáltatási végpontot](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) is alkalmazhat, bár ez a konfiguráció csak a csomag elérésére és a szolgáltatással való kommunikációra vonatkozik.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Lépésről lépésre, egyéni vendég-konfiguráció naplózási szabályzatának létrehozása Windows rendszerhez

A beállítások naplózásához hozzon létre egy DSC-konfigurációt. A következő PowerShell-parancsfájl egy **AuditBitLocker**nevű konfigurációt hoz létre, importálja a **PsDscResources** erőforrás-modult, és az `Service` erőforrás használatával naplózza a futó szolgáltatást. A konfigurációs parancsfájl futtatható Windows vagy macOS rendszerű gépről.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker ./Config
```

Mentse ezt a fájlt a `config.ps1` Project mappában található néven. Futtassa a PowerShellben a terminálon `./config.ps1` történő végrehajtással. A rendszer létrehoz egy új MOF-fájlt.

A `Node AuditBitlocker` parancs nem szükséges technikailag, `AuditBitlocker.mof` `localhost.mof`de az alapértelmezett helyett egy nevű fájlt hoz létre. Ha a. MOF-fájlnevet követi, a konfiguráció megkönnyíti a sok fájl rendszerezését nagy léptékű működés esetén.

A MOF lefordítása után a támogató fájlokat össze kell csomagolni. A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához.

A `New-GuestConfigurationPackage` parancsmag létrehozza a csomagot. A konfigurációhoz szükséges moduloknak elérhetőnek kell lenniük a- `$Env:PSModulePath`ben. A `New-GuestConfigurationPackage` parancsmag paraméterei Windows-tartalom létrehozásakor:

- **Name**: vendég konfigurációs csomag neve.
- **Konfiguráció**: LEfordított DSC-konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: kimeneti mappa elérési útja. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.

A következő parancs futtatásával hozzon létre egy csomagot az előző lépésben megadott konfiguráció használatával:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

A konfigurációs csomag létrehozása után, de az Azure-ba való közzététel előtt tesztelheti a csomagot a munkaállomás vagy CI/CD-környezetből. A GuestConfiguration parancsmag `Test-GuestConfigurationPackage` ugyanazt az ügynököt tartalmazza a fejlesztői környezetben, mint amelyet az Azure-gépeken belül használ. Ezzel a megoldással helyileg végezheti el az integrációs tesztelést, mielőtt kiadná a számlázott felhőalapú környezeteket.

Mivel az ügynök ténylegesen kiértékeli a helyi környezetet, a legtöbb esetben a test-parancsmagot ugyanarra az operációsrendszer-platformra kell futtatnia, mint a naplózást. A teszt csak a tartalmi csomagban található modulokat fogja használni.

A `Test-GuestConfigurationPackage` parancsmag paraméterei:

- **Név**: a vendég konfigurációs szabályzatának neve.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.

Futtassa a következő parancsot az előző lépésben létrehozott csomag teszteléséhez:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

A parancsmag a PowerShell-folyamatból is támogatja a bemenetet. A `New-GuestConfigurationPackage` parancsmag kimenetének átadása `Test-GuestConfigurationPackage` a parancsmagnak.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

A következő lépés a fájl közzététele a blob Storage-ban. Az alábbi szkript a feladat automatizálásához használható függvényt tartalmaz. A `publish` függvényben használt parancsokhoz a `Az.Storage` modul szükséges.

```azurepowershell-interactive
function publish {
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

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Miután létrehozta és feltöltötte a vendég konfigurációhoz tartozó egyéni házirend-csomagot, hozza létre a vendég-konfigurációs házirend definícióját. A `New-GuestConfigurationPolicy` parancsmag egy egyéni házirend-csomagot vesz fel, és létrehoz egy házirend-definíciót.

A `New-GuestConfigurationPolicy` parancsmag paraméterei:

- **ContentUri**: nyilvános http (s) URI a vendég konfigurációs tartalomkezelő csomaghoz.
- **DisplayName**: házirend megjelenítendő neve.
- **Leírás**: szabályzat leírása.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Verzió**: szabályzat verziója.
- **Elérési út**: a célhely elérési útja, ahol a szabályzat-definíciók létrejönnek
- **Platform**: cél platform (Windows/Linux) a vendég konfigurációs házirendhez és a tartalmi csomaghoz.

A következő példa egy egyéni házirend-csomagból egy megadott elérési úton hozza létre a házirend-definíciókat:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

A következő fájlokat hozza létre `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a házirend-fájlok kezdeményezésének megjelenítendő nevét és elérési útját tartalmazza.

Végül tegye közzé a szabályzat-definíciókat `Publish-GuestConfigurationPolicy` a parancsmag használatával. A parancsmag csak a **path** paraméterrel rendelkezik, amely a által `New-GuestConfigurationPolicy`létrehozott JSON-fájlok helyére mutat.

A közzétételi parancs futtatásához hozzá kell férnie a szabályzatok létrehozásához az Azure-ban. A konkrét engedélyezési követelmények dokumentálva vannak a [Azure Policy áttekintés](../overview.md) oldalon. A legjobb beépített szerepkör az erőforrás- **házirend közreműködője**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

A `Publish-GuestConfigurationPolicy` parancsmag fogadja az elérési utat a PowerShell-folyamatból. Ez a funkció azt jelenti, hogy létrehozhatja a házirend-fájlokat, és közzéteheti őket egyetlen vezetékes parancsban.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Az Azure-ban létrehozott szabályzattal az utolsó lépés a kezdeményezés kiosztása. Lásd: a kezdeményezés társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

> [!IMPORTANT]
> A _AuditIfNotExists_ -és _DeployIfNotExists_ -házirendeket egyesítő kezdeményezéssel **mindig** hozzá kell rendelni a vendég-konfigurációs házirendeket. Ha csak a _AuditIfNotExists_ szabályzat van hozzárendelve, az előfeltételek nincsenek telepítve, és a házirend mindig azt mutatja, hogy a "0" kiszolgálók megfelelőek.

A _DeployIfNotExists_ -effektussal rendelkező szabályzat-definíció hozzárendeléséhez további hozzáférési szint szükséges. A legalacsonyabb jogosultság megadásához létrehozhat egy egyéni szerepkör-definíciót, amely kibővíti az **erőforrás-házirend közreműködőjét**. Az alábbi példa létrehoz egy erőforrás- **házirend közreműködője** nevű szerepkört a _Microsoft. Authorization/roleAssignments/Write_további engedélyeivel.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Paraméterek használata az egyéni vendég-konfigurációs házirendekben

A vendég konfiguráció futási időben támogatja a konfiguráció felülírási tulajdonságait. Ez a funkció azt jelenti, hogy a csomagban lévő MOF-fájlban lévő értékeket nem kell statikusnak tekinteni. A felülbírálási értékek a Azure Policyon keresztül érhetők el, és nem befolyásolják a konfigurációk létrehozási vagy fordítási módját.

A parancsmagok `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` tartalmazzák a **Paraméterek**nevű paramétert. Ez a paraméter egy szórótábla-definíciót vesz fel, amely tartalmazza az egyes paraméterek részleteit, és létrehozza a Azure Policy-definícióhoz használt minden fájl szükséges részeit.

Az alábbi példa egy szabályzat-definíciót hoz létre egy szolgáltatás naplózásához, ahol a felhasználó a szabályzat-hozzárendelés időpontjában kiválasztja a listából.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Szabályzat életciklusa

Ha a szabályzat frissítését szeretné kibocsátani, két, figyelmet igénylő mező szükséges.

- **Verzió**: a `New-GuestConfigurationPolicy` parancsmag futtatásakor meg kell adnia a jelenleg közzétett verziónál nagyobb verziószámot. A tulajdonság frissíti a vendég konfiguráció-hozzárendelés verzióját, hogy az ügynök felismeri a frissített csomagot.
- **contentHash**: ezt a tulajdonságot a `New-GuestConfigurationPolicy` parancsmag automatikusan frissíti. Ez a csomag által `New-GuestConfigurationPackage`létrehozott kivonatoló érték. A tulajdonságnak megfelelőnek kell lennie a `.zip` közzétett fájlhoz. Ha csak a **contentUri** tulajdonság frissül, a bővítmény nem fogadja el a csomag tartalmát.

Egy frissített csomag kiadásának legegyszerűbb módja, ha megismétli a jelen cikkben ismertetett folyamatot, és megadja a verziószámot. Ez a folyamat garantálja az összes tulajdonság megfelelő frissítését.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows Csoportházirend tartalom konvertálása Azure Policy vendég konfigurációra

A vendég konfigurációja a Windows rendszerű gépek naplózásakor a PowerShell desired State Configuration szintaxisának implementációja. A DSC-Közösség közzétette az exportált Csoportházirend-sablonok DSC formátumra való konvertálásának eszközét. Az eszköznek a fent ismertetett vendég konfigurációs parancsmagokkal együtt történő használatával átalakíthatja a Windows Csoportházirend tartalmát, és becsomagolhatja vagy közzéteheti a Azure Policy a naplózáshoz. További információ az eszköz használatáról [: a csoportházirend átalakítása a DSC-be](/powershell/scripting/dsc/quickstarts/gpo-quickstart)című cikk rövid útmutatója.
A tartalom konvertálása után a fenti lépéseket követve hozzon létre egy csomagot, és tegye közzé Azure Policyként, ugyanúgy, mint bármely DSC-tartalomhoz.

## <a name="optional-signing-guest-configuration-packages"></a>Nem kötelező: a vendég konfigurációs csomagjainak aláírása

A vendég konfigurációja egyéni házirendek SHA256 kivonatot használnak a házirend-csomag érvényesítéséhez.
Igény szerint az ügyfelek is használhatnak tanúsítványokat a csomagok aláírására és a vendég konfigurációs bővítmény kényszerítésére, hogy csak az aláírt tartalmat engedélyezzék.

Ennek a forgatókönyvnek az engedélyezéséhez két lépést kell végrehajtania. Futtassa a parancsmagot a tartalomindex aláírásához, és fűzze hozzá a címkét azokhoz a gépekhez, amelyeknek az aláírásához kód szükséges.

Az aláírás-ellenőrzési funkció használatához futtassa a `Protect-GuestConfigurationPackage` parancsmagot a csomag aláírásához a közzététel előtt. Ehhez a parancsmaghoz "kód aláírása" tanúsítvány szükséges.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

A `Protect-GuestConfigurationPackage` parancsmag paraméterei:

- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.
- **Tanúsítvány**: kód aláírására szolgáló tanúsítvány a csomag aláírásához. Ez a paraméter csak Windows-tartalmak aláírása esetén támogatott.

A GuestConfiguration-ügynök elvárja, hogy a tanúsítvány nyilvános kulcsa a Windows rendszerű gépeken és a Linux-gépek elérési útjában `/usr/local/share/ca-certificates/extra` legyen jelen a "megbízható legfelső szintű hitelesítésszolgáltatók" szolgáltatásban. Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, az egyéni házirend alkalmazása előtt telepítse a tanúsítvány nyilvános kulcsát a gépre. Ez a folyamat a virtuális gépen belüli bármely technikával vagy Azure Policy használatával végezhető el. [Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)található egy példa erre a sablonra.
Az Key Vault hozzáférési szabályzatnak lehetővé kell tennie a számítási erőforrás-szolgáltató számára a tanúsítványok elérését a központi telepítések során. A részletes lépésekért lásd: [Key Vault beállítása virtuális gépekhez a Azure Resource Managerban](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Az alábbi példa a nyilvános kulcs exportálását írja elő egy aláíró tanúsítványból a gépre való importáláshoz.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

A tartalom közzététele után fűzze hozzá a nevet `GuestConfigPolicyCertificateValidation` és az értéket `enabled` tartalmazó címkét az összes olyan virtuális géphez, amelynél szükség van a kód aláírására. Tekintse meg azokat a [mintákat](../samples/built-in-policies.md#tags) , amelyekkel a címkék a Azure Policy használatával méretezhetők. A címke betartása után a `New-GuestConfigurationPolicy` parancsmag használatával generált szabályzat-definíció engedélyezi a követelményt a vendég konfigurációs bővítményen keresztül.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>A vendég-konfigurációs házirend hozzárendeléseinek hibaelhárítása (előzetes verzió)

Egy eszköz előzetes verzióban érhető el, amely segítséget nyújt az Azure Policy vendég konfigurációs hozzárendeléseinek hibaelhárításában. Az eszköz előzetes verzióban érhető el, és közzé lett téve a PowerShell-galéria mint modul neve [vendég konfigurációs hibakereső](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Az eszköz parancsmagokkal kapcsolatos további információkért használja a PowerShell Get-Help parancsát a beépített útmutatás megjelenítéséhez. Mivel az eszköz gyakori frissítéseket kap, ez a legjobb módszer a legfrissebb információk beszerzésére.

## <a name="next-steps"></a>További lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](get-compliance-data.md).
