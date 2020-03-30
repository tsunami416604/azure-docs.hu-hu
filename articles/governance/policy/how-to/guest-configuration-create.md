---
title: Vendégkonfigurációs házirendek létrehozása a Windows rendszerhez
description: Ismerje meg, hogyan hozhat létre egy Azure Policy Vendég konfigurációs szabályzatot a Windows hoz létre.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24069ff6518c4244026378e48216d4568fffeb8a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365472"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Vendégkonfigurációs házirendek létrehozása a Windows rendszerhez

Egyéni szabályzatok létrehozása előtt érdemes elolvasni az általános áttekintést az [Azure Policy Vendégkonfiguráció](../concepts/guest-configuration.md)lapján.
 
A Vendégkonfigurációs házirendek Linuxhoz való létrehozásáról a [Vendégkonfigurációs házirendek létrehozása Linuxhoz című](./guest-configuration-create-linux.md) oldalon olvashat.

A Windows naplózásakor a Vendég konfiguráció a [Kívánt állapotkonfiguráció](/powershell/scripting/dsc/overview/overview) (DSC) erőforrásmodult és konfigurációs fájlt használja. A DSC konfiguráció határozza meg azt a feltételt, amelyben a gépnek lennie kell.
Ha a konfiguráció kiértékelése sikertelen, a **házirend-effektus-naplózásHa nem létezik,** és a számítógép **nem megfelelőnek**minősül.

[Az Azure Policy vendégkonfigurációcsak](../concepts/guest-configuration.md) a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások helyreállítása még nem érhető el.

A következő műveletek segítségével hozzon létre saját konfigurációt egy Azure vagy nem Azure-gép állapotának érvényesítéséhez.

> [!IMPORTANT]
> A Vendégkonfigurációval rendelkező egyéni házirendek előzetes verziójú funkciók.

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Vendégkonfigurációs összetevő létrehozása, a műtermék automatikus tesztelése, a házirend-definíció létrehozása és a házirend közzététele teljes mértékben automatizálható a PowerShell Vendég konfigurációs moduljával. A modul telepíthető windowsos, macOS vagy Linux rendszert futtató, helyileg futó PowerShell 6.2-es vagy újabb verziós gépen, vagy az [Azure Cloud Shell](https://shell.azure.com)használatával, illetve az Azure [PowerShell Core Docker-lemezképpel.](https://hub.docker.com/r/azuresdk/azure-powershell-core)

> [!NOTE]
> A konfigurációk összeállítása még nem támogatott Linuxon.

### <a name="base-requirements"></a>Alapszintű követelmények

Operációs rendszerek, ahol a modul telepíthető:

- Linux
- macOS
- Windows

A Vendég konfigurációja erőforrásmodulhoz a következő szoftver szükséges:

- PowerShell 6.2-es vagy újabb verzió. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).
  - Csak az AZ modulok "Az.Accounts" és "Az.Resources" van szükség.

### <a name="install-the-module"></a>A modul telepítése

A **GuestConfiguration** modul telepítése a PowerShellben:

1. Egy PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. A modul importálásának ellenőrzése:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Vendégkonfigurációs összetevők és házirend a Windows rendszerhez

Vendég konfiguráció a PowerShell kívánt állapotkonfiguráció, mint a nyelvi absztrakció írásához, hogy mit kell naplózni a Windows. Az ügynök betölti a PowerShell 6.2 önálló példányát, így nincs ütköző a PowerShell DSC használatával a Windows PowerShell 5.1-ben, és nincs követelmény a PowerShell 6.2-es vagy újabb verzióelőzetes telepítéséhez.

A DSC-fogalmak és terminológia áttekintését a [PowerShell DSC – áttekintés című témakörben találja.](/powershell/scripting/dsc/overview/overview)

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>A vendégkonfigurációs modulok eltérése a Windows PowerShell DSC-moduloktól

Amikor a Vendég konfiguráció naplózza `Test-TargetResource` a gépet, először fut annak megállapítására, hogy a megfelelő állapotban van-e. A függvény által visszaadott logikai érték határozza meg, hogy a vendég-hozzárendelés Azure Resource Manager-állapota megfelelő/nem megfelelő legyen-e. Ezután a `Get-TargetResource` szolgáltató fut, hogy visszatérjen az aktuális állapotát az egyes beállítások, így a részletek is elérhetők arról, hogy miért a számítógép nem megfelelő, vagy annak ellenőrzésére, hogy az aktuális állapot megfelelő.

### <a name="get-targetresource-requirements"></a>Get-TargetResource követelmények

A `Get-TargetResource` függvény speciális követelményeket támaszt a vendégkonfigurációval kapcsolatban, amelyre nincs szükség a Kívánt Windows-konfigurációhoz.

- A visszaadott kivonatnak tartalmaznia kell egy Reasons nevű **tulajdonságot.**
- Az Okok tulajdonságnak tömbnek kell lennie.
- A tömb minden elemének kivonatolónak kell **lennie, kód** és **kifejezés**nevű kulcsokkal.

Az okok tulajdonságot a szolgáltatás arra használja, hogy egységesítse az információk bemutatásának módját, ha egy gép nem felel meg a követelményeknek. Az Okok részben minden egyes elemre "okként" is feltud tekinteni arra, hogy az erőforrás nem megfelelő. A tulajdonság egy tömb, mert egy erőforrás lehet, hogy nem megfelelő több okból.

A **szolgáltatás** a Kód és **kifejezés** tulajdonságokat várja el. Egyéni erőforrás készítésekekekekénél állítsa be azt a szöveget (általában stdout), amelyet a **Kifejezés**értékeként nem megfelelő ként szeretne megjeleníteni. **A kódnak** speciális formázási követelményei vannak, így a jelentéskészítés egyértelműen megjelenítheti a naplózáshoz használt erőforrás adatait. Ez a megoldás bővíthetővé teszi a Vendégkonfigurációt. Bármilyen parancs futtatható mindaddig, amíg a kimenet karakterlánc-értékként visszaadható a **Phrase** tulajdonsághoz.

- **Kód** (karakterlánc): Az erőforrás neve, ismétlődő, majd egy rövid név szóközök nélkül az ok azonosítójaként. Ezt a három értéket szóköz öklével körülhatároltnak kell lennie.
  - Egy példa lenne`registry:registry:keynotpresent`
- **Kifejezés** (karakterlánc): Ember által olvasható szöveg, amely megmagyarázza, hogy a beállítás miért nem megfelelő.
  - Egy példa lenne`The registry key $key is not present on the machine.`

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
### <a name="configuration-requirements"></a>Konfigurációs követelmények

Az egyéni konfiguráció nevének mindenhol konzisztensnek kell lennie. A tartalomcsomag .zip fájljának nevének, a MOF-fájlkonfigurációs nevének és az Erőforrás-kezelő sablonban szereplő vendéghozzárendelés nevének meg kell egyeznie.

### <a name="scaffolding-a-guest-configuration-project"></a>Vendégkonfigurációs projekt állványzata

Azok a fejlesztők, akik szeretnék felgyorsítani az első lépések és a mintakódból végzett munkát, telepíthetnek egy Vendég konfigurációs projekt nevű közösségi **projektet.** A projekt telepítegy sablont a [Gipsz](https://github.com/powershell/plaster) PowerShell modulhoz. Ez az eszköz használható állványzat egy projekt, beleértve a működő konfiguráció és minta erőforrás, és egy sor [Pester](https://github.com/pester/pester) tesztek érvényesíteni a projektet. A sablon a Visual Studio-kód feladatfutóit is tartalmazza az épület létrehozásának és a Vendég konfigurációs csomag érvényesítésének automatizálásához. További információt a GitHub-projekt [vendégkonfigurációs projektjében talál.](https://github.com/microsoft/guestconfigurationproject)

A konfigurációk általános működéséről további információt az [Írás, fordítás és Konfiguráció alkalmazása című](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)témakörben talál.

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Vendégkonfigurációs összetevő várt tartalma

A kitöltött csomagot a Vendég konfiguráció az Azure Policy-definíciók létrehozásához használja. A csomag a következőkből áll:

- A lefordított DSC-konfiguráció MOF-ként
- Modulok mappa
  - GuestConfiguration modul
  - DscNativeResources modul
  - (Windows) A MOF által igényelt DSC erőforrásmodulok

A PowerShell-parancsmagok segítséget nyújtanak a csomag létrehozásában.
Nincs szükség gyökérszintű mappára vagy verziómappára.
A csomagformátumnak .zip fájlnak kell lennie.

### <a name="storing-guest-configuration-artifacts"></a>Vendégkonfigurációs összetevők tárolása

A .zip csomagot olyan helyen kell tárolni, amely a felügyelt virtuális gépek számára elérhető.
Ilyenek például a GitHub-adattárak, egy Azure-tárház vagy az Azure storage. Ha nem szeretné, hogy a csomag nyilvános, [sas-jogkivonatot](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) is megadhat az URL-címben.
A [szolgáltatásvégpontot](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) magánhálózatban lévő gépekhez is megvalósíthatja, bár ez a konfiguráció csak a csomag elérésére vonatkozik, és nem kommunikál a szolgáltatással.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Lépésről lépésre, egyéni vendégkonfiguráció-naplózási házirend létrehozása a Windows rendszerhez

Hozzon létre egy DSC-konfigurációt. A következő PowerShell-parancsfájl-példa létrehoz egy **AuditBitLocker**nevű konfigurációt, importálja `Service` a **PsDscResources erőforrásmodult,** és az erőforrást használja egy futó szolgáltatás naplózásához. A konfigurációs parancsfájl Windows vagy macOS rendszerű gépekről hajtható végre.

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
AuditBitLocker -out ./Config
```

A `Node AuditBitlocker` parancs technikailag nem kötelező, de az `AuditBitlocker.mof` alapértelmezett helyett `localhost.mof`elnevezett fájlt hoz létre. Miután a .mof fájl neve követi a konfiguráció megkönnyíti a rendszerezéssok fájlt, ha működik a skála.

A MOF fordítása után a segédfájlokat össze kell csomagolni. A kitöltött csomagot a Vendég konfiguráció az Azure Policy-definíciók létrehozásához használja.

A `New-GuestConfigurationPackage` parancsmag hozza létre a csomagot. A parancsmag `New-GuestConfigurationPackage` paraméterei A Windows-tartalom létrehozásakor:

- **Név**: Vendég konfigurációs csomag neve.
- **Konfiguráció**: A DSC konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: Kimeneti mappa elérési útja. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.

A következő parancs futtatásával hozzon létre egy csomagot az előző lépésben megadott konfigurációhasználatával:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

A konfigurációs csomag létrehozása után, de az Azure-ban való közzététele előtt tesztelheti a csomagot a munkaállomásról vagy ci/CD-környezetből. A GuestConfiguration parancsmag `Test-GuestConfigurationPackage` ugyanazt az ügynököt tartalmazza a fejlesztői környezetben, mint az Azure-gépeken. Ezzel a megoldással helyileg is elvégezheti az integrációtesztelést, mielőtt a számlázott felhőalapú környezetekben feladná.

Mivel az ügynök ténylegesen kiértékeli a helyi környezetben, a legtöbb esetben a teszt-parancsmag futtatásához ugyanazon az operációs platformon, mint azt tervezi, hogy a naplózás.

A `Test-GuestConfigurationPackage` parancsmag paraméterei:

- **Név**: Vendégkonfigurációs házirend neve.
- **Paraméter**: A házirend paraméterei kivonatoló formátumban vannak megadva.
- **Elérési út**: A Vendég konfigurációs csomag teljes elérési útja.

Futtassa a következő parancsot az előző lépés által létrehozott csomag teszteléséhez:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

A parancsmag a PowerShell-folyamat bemenetét is támogatja. A parancsmag `New-GuestConfigurationPackage` kimenetét a `Test-GuestConfigurationPackage` parancsmaghoz kell vezetni.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

A következő lépés a fájl közzététele a blob storage.The next step is to publish the file to blob storage. Az alábbi parancsfájl tartalmaz egy függvényt, amelynek segítségével automatizálhatja ezt a feladatot. A `publish` függvényben használt parancsokhoz a `Az.Storage` modul szükséges.

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

Miután létrehozott és feltöltött egy vendégkonfigurációs egyéni házirendcsomagot, hozza létre a Vendég konfigurációs házirend-definíciót. A `New-GuestConfigurationPolicy` parancsmag egy egyéni házirendcsomagot vesz, és létrehoz egy házirend-definíciót.

A `New-GuestConfigurationPolicy` parancsmag paraméterei:

- **ContentUri**: A Vendég konfigurációtartalom-csomag nyilvános http-uri-ja.
- **Megjelenítendő:** Házirend megjelenítendő neve.
- **Leírás**: A házirend leírása.
- **Paraméter**: A házirend paraméterei kivonatoló formátumban vannak megadva.
- **Verzió**: Házirend verziója.
- **Elérési út**: A házirend-definíciók létrehozásának célelérési útja.
- **Platform**: Célplatform (Windows/Linux) a vendégkonfigurációs házirendhez és tartalomcsomaghoz.

A következő példa egy megadott elérési út házirend-definícióit hozza létre egy egyéni házirendcsomagból:

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

A következő fájlokat `New-GuestConfigurationPolicy`hozta létre:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Kezdeményezés.json**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a kezdeményezés megjelenítendő nevét és elérési útját tartalmazza a házirendfájlokban.

Végül tegye közzé a házirend-definíciókat a `Publish-GuestConfigurationPolicy` parancsmag használatával. A parancsmag csak a **Path** paraméterrel rendelkezik, amely a. `New-GuestConfigurationPolicy`által létrehozott JSON-fájlok helyére mutat.

A Közzététel parancs futtatásához hozzáférésre van szüksége a szabályzatok létrehozásához az Azure-ban. A konkrét engedélyezési követelmények dokumentálva vannak az [Azure-szabályzat áttekintése](../overview.md) oldalon. A legjobb beépített szerepkör az **erőforrás-házirend közreműködője**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

A `Publish-GuestConfigurationPolicy` parancsmag elfogadja az elérési utat a PowerShell-folyamatból. Ez a szolgáltatás azt jelenti, hogy létrehozhatja a házirendfájlokat, és közzéteheti őket egyetlen vezetékes parancskészletben.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Az Azure-ban létrehozott szabályzattal az utolsó lépés a kezdeményezés hozzárendelése. Megtudhatja, hogyan rendelheti hozzá a kezdeményezést a [Portal,](../assign-policy-portal.md)az [Azure CLI](../assign-policy-azurecli.md)és az [Azure PowerShell](../assign-policy-powershell.md)szolgáltatáshoz.

> [!IMPORTANT]
> A vendégkonfigurációs házirendeket **mindig** az _AuditIfNotExists_ és a _DeployIfNotExists_ házirendeket egyesítő kezdeményezés használatával kell hozzárendelni. Ha csak az _AuditIfNotExists_ házirend van hozzárendelve, az előfeltételek nincsenek üzembe helyezve, és a házirend mindig azt mutatja, hogy a "0" kiszolgálók megfelelőek.

A házirend-definíció _deployIfNotExists_ effektussal történő hozzárendelése további hozzáférési szintet igényel. A legkevesebb jogosultság biztosításához létrehozhat egy egyéni szerepkör-definíciót, amely kiterjeszti **az erőforrás-házirend közreműködőjét.** Az alábbi példa létrehoz egy **Erőforrás-házirend közreműködő DINE** nevű szerepkört a _Microsoft.Authorization/roleAssignments/write_további engedélyével.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Paraméterek használata egyéni vendégkonfigurációs házirendekben

A Vendégkonfiguráció támogatja a konfiguráció futási idejű felülbírálási tulajdonságait. Ez a funkció azt jelenti, hogy a csomagMOF-fájlban lévő értékeket nem kell statikusnak tekinteni. A felülbírálási értékek az Azure Policy-en keresztül vannak megadva, és nincsenek hatással a konfigurációk szerzői vagy fordítási módjára.

A `New-GuestConfigurationPolicy` parancsmagok, `Test-GuestConfigurationPolicyPackage` és tartalmaznak egy **paraméterek**nevű paramétert . Ez a paraméter egy kivonatoló definíciót vesz fel, amely tartalmazza az egyes paraméterek összes részletét, és létrehozza az Azure Policy-definícióhoz használt minden egyes fájl szükséges szakaszait.

A következő példa létrehoz egy házirend-definíciót egy szolgáltatás naplózásához, ahol a felhasználó a házirend-hozzárendelés időpontjában kiválaszt egy listából.

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

## <a name="policy-lifecycle"></a>A szabályzat életciklusa

Ha frissíteni szeretné a házirendet, két mező van, amelyek figyelmet igényelnek.

- **Verzió:** A parancsmag futtatásakor meg `New-GuestConfigurationPolicy` kell adnia a jelenleg közzétettverziószámnál nagyobb verziószámot. A tulajdonság frissíti a Vendég konfiguráció hozzárendelés verzióját, így az ügynök felismeri a frissített csomagot.
- **contentHash**: Ezt a tulajdonságot a `New-GuestConfigurationPolicy` parancsmag automatikusan frissíti. Ez a csomag kivonatértéke, amelyet `New-GuestConfigurationPackage`a. A tulajdonságnak helyesnek `.zip` kell lennie a közzétett fájlhoz. Ha csak a **contentUri** tulajdonság frissül, a bővítmény nem fogadja el a tartalomcsomagot.

A frissített csomagok kiadásának legegyszerűbb módja a cikkben leírt folyamat megismétlése és a frissített verziószám megadásával. Ez a folyamat garantálja, hogy az összes tulajdonság megfelelően lett frissítve.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows csoportházirend-tartalom konvertálása az Azure Policy vendégkonfigurációjává

Vendég konfiguráció, Windows-gépek naplózásakor, a PowerShell kívánt állapotkonfigurációs szintaxisának megvalósítása. A DSC-közösség olyan eszközt tett közzé, amelyből az exportált csoportházirend-sablonokdsc formátumba konvertálhatók. Az eszköz használatával együtt a vendég konfigurációparancsait a fent leírt, konvertálhatja a Windows csoportházirend tartalmát, és csomag/közzé az Azure-szabályzat naplózásához. Az eszköz használatáról a [Rövid útmutató: Csoportházirend konvertálása DSC-vé](/powershell/scripting/dsc/quickstarts/gpo-quickstart)című témakörben olvashat.
A tartalom konvertálása után a fenti lépéseket, hogy hozzon létre egy csomagot, és tegye közzé az Azure Policy ugyanazok, mint bármely DSC-tartalom.

## <a name="optional-signing-guest-configuration-packages"></a>Nem kötelező: Vendégkonfigurációs csomagok aláírása

Vendég konfiguráció egyéni házirendek használata SHA256 kivonat a házirendcsomag ellenőrzése nem változott.
Szükség esetén az ügyfelek is használhatnak tanúsítványt a csomagok aláírására, és kényszeríthetik a Vendég konfigurációbővítményt, hogy csak aláírt tartalmat engedélyezze.

A forgatókönyv engedélyezéséhez két lépést kell végrehajtania. Futtassa a parancsmabát a tartalomcsomag aláírásához, és fűzze hozzá a címkét a gépekhez, amelyhez kódot kell aláírni.

Az Aláírás-érvényesítés szolgáltatás `Protect-GuestConfigurationPackage` használatához futtassa a parancsmabkal a csomag közzététel előtti aláírásához. Ehhez a parancsmaghoz "Kódaláírás" tanúsítvány szükséges.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

A `Protect-GuestConfigurationPackage` parancsmag paraméterei:

- **Elérési út**: A Vendég konfigurációs csomag teljes elérési útja.
- **Tanúsítvány**: Kódaláíró tanúsítvány a csomag aláírásához. Ez a paraméter csak a Windows tartalom aláírásakor támogatott.

A GuestConfiguration ügynök elvárja, hogy a tanúsítvány nyilvános kulcsa jelen legyen `/usr/local/share/ca-certificates/extra` a "Megbízható legfelső szintű hitelesítésszolgáltatók" rendszerben Windows-gépeken és Linux-gépeken. Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, telepítse a tanúsítvány nyilvános kulcsát a számítógépre az egyéni házirend alkalmazása előtt. Ez a folyamat a virtuális gépen belüli bármilyen technikával vagy az Azure Policy használatával végezhető el. Itt egy példasablon [található.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)
A Key Vault hozzáférési szabályzatának lehetővé kell tennie, hogy a számítási erőforrás-szolgáltató a központi telepítés során hozzáférjen a tanúsítványokhoz. A részletes lépéseket a [Key Vault beállítása virtuális gépekhez az Azure Resource Managerben című témakörben található.](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)

Az alábbiakban egy példa a nyilvános kulcsot egy aláíró tanúsítványból exportálja, hogy importálja a gépre.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

A tartalom közzététele után csatoljon egy `GuestConfigPolicyCertificateValidation` nevet `enabled` és értéket képviselő címkét minden olyan virtuális géphez, ahol kódaláírásra van szükség. Tekintse meg a [címke mintákat,](../samples/built-in-policies.md#tags) hogyan címkéket lehet szállítani az Azure Policy használatával. Ha ez a címke a helyén van, `New-GuestConfigurationPolicy` a parancsmag használatával létrehozott házirend-definíció lehetővé teszi a követelményt a Vendég konfigurációbővítményen keresztül.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Vendégkonfigurációs házirend-hozzárendelések hibaelhárítása (előzetes verzió)

Egy eszköz előzetes verzióban érhető el az Azure Policy Vendégkonfiguráció-hozzárendelések hibaelhárításához. Az eszköz előzetes verzióban érhető el, és a PowerShell-galériában [vendégkonfigurációs hibaelhárítóként](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)lett közzétéve.

Az eszköz parancsmagjairól a PowerShell Súgó parancsával további információt kaphat a beépített útmutatás megjelenítéséhez. Mivel az eszköz egyre gyakori frissítéseket, ez a legjobb módja annak, hogy a legfrissebb információkat.

## <a name="next-steps"></a>További lépések

- További információ a vendégkonfigurációval rendelkező virtuális gépek [naplózásáról.](../concepts/guest-configuration.md)
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](get-compliance-data.md)
