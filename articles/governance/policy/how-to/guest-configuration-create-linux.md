---
title: Vendégkonfigurációs házirendek létrehozása Linuxhoz
description: Ismerje meg, hogyan hozhat létre egy Azure Policy Vendég konfigurációs szabályzat linuxos.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 219b38bd81cae8d16241d1ee16cfdd2f400ae91e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024982"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Vendégkonfigurációs házirendek létrehozása Linuxhoz

Egyéni szabályzatok létrehozása előtt olvassa el az áttekintő információkat az [Azure Policy Vendégkonfiguráció című](../concepts/guest-configuration.md)témakörben.
 
A Vendégkonfigurációs házirendek Windows-hoz való létrehozásáról a [Vendégkonfigurációs házirendek létrehozása a Windows rendszerhez című](./guest-configuration-create.md) lapon olvashat.

Linux naplózásakor a Vendég konfiguráció [a Chef InSpec-et](https://www.inspec.io/)használja. Az InSpec profil határozza meg azt a feltételt, amelyben a gépnek lennie kell. Ha a konfiguráció kiértékelése sikertelen, a **házirend-effektus-naplózásHa nem létezik,** és a számítógép **nem megfelelőnek**minősül.

[Az Azure Policy vendégkonfigurációcsak](../concepts/guest-configuration.md) a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások helyreállítása még nem érhető el.

A következő műveletek segítségével hozzon létre saját konfigurációt egy Azure vagy nem Azure-gép állapotának érvényesítéséhez.

> [!IMPORTANT]
> A Vendégkonfigurációval rendelkező egyéni házirendek előzetes verziójú funkciók.
>
> A vendégkonfiguráció bővítmény az Azure virtuális gépeken végzett naplózáshoz szükséges.
> A bővítmény összes Linux-gépen történő üzembe helyezéséhez rendelje hozzá a következő házirend-definíciót:
>   - [A vendégkonfigurációs házirend linuxos virtuális gépeken való engedélyezéséhez telepítse az előfeltételeket.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb27e9e0-526e-4ae1-89f2-a2a0bf0f8a50)

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Vendégkonfigurációs összetevő létrehozása, a műtermék automatikus tesztelése, a házirend-definíció létrehozása és a házirend közzététele teljes mértékben automatizálható a PowerShell Vendég konfigurációs moduljával. A modul telepíthető windowsos, macOS vagy Linux rendszert futtató, helyileg futó PowerShell 6.2-es vagy újabb verziós gépen, vagy az [Azure Cloud Shell](https://shell.azure.com)használatával, illetve az Azure [PowerShell Core Docker-lemezképpel.](https://hub.docker.com/r/azuresdk/azure-powershell-core)

> [!NOTE]
> A konfigurációk összeállítása linuxos kapcsolatnem támogatott.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Vendégkonfigurációs összetevők és Linux-házirend

Még Linux-környezetekben is a Vendég konfiguráció a kívánt állapotkonfigurációt használja nyelvi absztrakcióként. A megvalósítás natív kódon (C++) alapul, így nem igényel PowerShell-t. Azonban ez nem igényel egy konfigurációs MOF a környezet részleteit leíró. A DSC az InSpec burkolójaként működik, hogy szabványosítsa a végrehajtás módját, a paraméterek módját és a kimenet szolgáltatásba való visszaadásának módját. Az egyéni InSpec-tartalom használata esetén kevés ismerete van szükség a DSC-ről.

#### <a name="configuration-requirements"></a>Konfigurációs követelmények

Az egyéni konfiguráció nevének mindenhol konzisztensnek kell lennie. A tartalomcsomag .zip fájljának nevének, a MOF-fájlkonfigurációs nevének és az Erőforrás-kezelő sablonban szereplő vendéghozzárendelés nevének meg kell egyeznie.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Egyéni vendégkonfiguráció konfiguráció linuxon

A Guest Configuration `ChefInSpecResource` on Linux az erőforrássegítségével adja meg a motornak az [InSpec-profil](https://www.inspec.io/docs/reference/profiles/)nevét. **A név** az egyetlen szükséges erőforrástulajdonság. Hozzon létre egy YaML-fájlt és egy Ruby parancsfájlt az alábbiakban részletezett módon.

Először hozza létre az InSpec által használt YaML-fájlt. A fájl alapvető információkat tartalmaz a környezetről. Egy példa az alábbiakban található:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Mentse a nevet `inspec.yml` tartalmazó fájlt `linux-path` a projektkönyvtárban megnevezett mappába.

Ezután hozza létre a Ruby fájlt a gép naplózásához használt InSpec nyelvi absztrakcióval.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Mentse a nevet `linux-path.rb` tartalmazó fájlt `controls` egy `linux-path` új, a könyvtárban elnevezett mappába.

Végül hozzon létre egy konfigurációt, importálja a **PSDesiredStateConfiguration** erőforrásmodult, és fordítsa le a konfigurációt.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Mentse ezt a `config.ps1` nevet tartalmazó fájlt a projekt mappába. Futtassa a PowerShell `./config.ps1` ben a terminálon végrehajtva. A program új mof fájlt hoz létre.

A `Node AuditFilePathExists` parancs technikailag nem kötelező, de az `AuditFilePathExists.mof` alapértelmezett helyett `localhost.mof`elnevezett fájlt hoz létre. Miután a .mof fájl neve követi a konfiguráció megkönnyíti a rendszerezéssok fájlt, ha működik a skála.



Most már rendelkeznie kell egy projektstruktúrával az alábbiak szerint:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

A segédfájlokat össze kell csomagolni. A kitöltött csomagot a Vendég konfiguráció az Azure Policy-definíciók létrehozásához használja.

A `New-GuestConfigurationPackage` parancsmag hozza létre a csomagot. A `New-GuestConfigurationPackage` parancsmag paraméterei Linux-tartalom létrehozásakor:

- **Név**: Vendég konfigurációs csomag neve.
- **Konfiguráció**: A fordítási dokumentum teljes elérési útja.
- **Elérési út**: Kimeneti mappa elérési útja. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.
- **ChefProfilePath**: Teljes elérési út az InSpec-profilhoz. Ez a paraméter csak akkor támogatott, ha linuxos naplózásra tartalom létrehozása.

A következő parancs futtatásával hozzon létre egy csomagot az előző lépésben megadott konfigurációhasználatával:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

A konfigurációs csomag létrehozása után, de az Azure-ban való közzététele előtt tesztelheti a csomagot a munkaállomásról vagy ci/CD-környezetből. A GuestConfiguration parancsmag `Test-GuestConfigurationPackage` ugyanazt az ügynököt tartalmazza a fejlesztői környezetben, mint az Azure-gépeken. Ezzel a megoldással helyileg végezhet integrációs tesztelést a számlázott felhőalapú környezetekben való kiadás előtt.

Mivel az ügynök ténylegesen kiértékeli a helyi környezetben, a legtöbb esetben a teszt-parancsmag futtatásához ugyanazon az operációs platformon, mint azt tervezi, hogy a naplózás.

A `Test-GuestConfigurationPackage` parancsmag paraméterei:

- **Név**: Vendégkonfigurációs házirend neve.
- **Paraméter**: A házirend paraméterei kivonatoló formátumban vannak megadva.
- **Elérési út**: A Vendég konfigurációs csomag teljes elérési útja.

Futtassa a következő parancsot az előző lépés által létrehozott csomag teszteléséhez:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

A parancsmag a PowerShell-folyamat bemenetét is támogatja. A parancsmag `New-GuestConfigurationPackage` kimenetét a `Test-GuestConfigurationPackage` parancsmaghoz kell vezetni.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

A következő fájlokat `New-GuestConfigurationPolicy`hozta létre:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Kezdeményezés.json**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a kezdeményezés megjelenítendő nevét és elérési útját tartalmazza a házirendfájlokban.

Végül tegye közzé a házirend-definíciókat a `Publish-GuestConfigurationPolicy` parancsmag használatával.
A parancsmag csak a **Path** paraméterrel rendelkezik, amely a. `New-GuestConfigurationPolicy`által létrehozott JSON-fájlok helyére mutat.

A Közzététel parancs futtatásához hozzáférésre van szüksége a szabályzatok létrehozásához az Azure-ban. A konkrét engedélyezési követelmények dokumentálva vannak az [Azure-szabályzat áttekintése](../overview.md) oldalon. A legjobb beépített szerepkör az **erőforrás-házirend közreműködője**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 A `Publish-GuestConfigurationPolicy` parancsmag elfogadja az elérési utat a PowerShell-folyamatból. Ez a szolgáltatás azt jelenti, hogy létrehozhatja a házirendfájlokat, és közzéteheti őket egyetlen vezetékes parancskészletben.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Az InSpec segítségével a paramétereket általában futásidőben vagy attribútumokat használó kódként kezeli a rendszer. Vendég konfiguráció elhomályosítja ezt a folyamatot, így a bemeneti lehet adni, ha a házirend van rendelve. A rendszer automatikusan attribútumfájlt hoz létre a számítógépen belül. Nem kell fájlt létrehoznia és hozzáadnia a projekthez. Két lépésből áll, hogy paramétereket adjon hozzá a Linux naplózási projekthez.

Adja meg a ruby-fájl bemenetét, ahol a számítógépen naplózni kívánt parancsfájlt. Az alábbiakban egy példa található.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

A `New-GuestConfigurationPolicy` parancsmagok, `Test-GuestConfigurationPolicyPackage` és tartalmaznak egy **paraméterek**nevű paramétert . Ez a paraméter egy kivonatot vesz fel, amely tartalmazza az egyes paraméterek összes részletét, és automatikusan létrehozza az egyes Azure-szabályzat-definíciók létrehozásához használt fájlok összes szükséges szakaszát.

A következő példa létrehoz egy házirend-definíciót egy fájlelérési út naplózásához, ahol a felhasználó megadja az elérési utat a házirend-hozzárendelés időpontjában.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

Linux-házirendek esetén adja meg a **tulajdonság AttributesYmlContent** a konfigurációban, és szükség szerint felülírja az értékeket. A Vendég konfigurációs ügynök automatikusan létrehozza az InSpec által az attribútumok tárolására használt YAML-fájlt. Lásd az alábbi példát.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>A szabályzat életciklusa

A házirend-definíció frissítésének kiadásához két mező van, amelyek figyelmet igényelnek.

- **Verzió:** A parancsmag futtatásakor meg `New-GuestConfigurationPolicy` kell adnia a jelenleg közzétettverziószámnál nagyobb verziószámot. A tulajdonság frissíti a Vendég konfiguráció hozzárendelés verzióját, így az ügynök felismeri a frissített csomagot.
- **contentHash**: Ezt a tulajdonságot a `New-GuestConfigurationPolicy` parancsmag automatikusan frissíti. Ez a csomag kivonatértéke, amelyet `New-GuestConfigurationPackage`a. A tulajdonságnak helyesnek `.zip` kell lennie a közzétett fájlhoz. Ha csak a **contentUri** tulajdonság frissül, a bővítmény nem fogadja el a tartalomcsomagot.

A frissített csomagok kiadásának legegyszerűbb módja a cikkben leírt folyamat megismétlése és a frissített verziószám megadásával. Ez a folyamat garantálja, hogy az összes tulajdonság megfelelően lett frissítve.

## <a name="optional-signing-guest-configuration-packages"></a>Nem kötelező: Vendégkonfigurációs csomagok aláírása

Vendég konfiguráció egyéni házirendek használata SHA256 kivonat a házirendcsomag ellenőrzése nem változott.
Szükség esetén az ügyfelek is használhatnak tanúsítványt a csomagok aláírására, és kényszeríthetik a Vendég konfigurációbővítményt, hogy csak aláírt tartalmat engedélyezze.

A forgatókönyv engedélyezéséhez két lépést kell végrehajtania. Futtassa a parancsmabát a tartalomcsomag aláírásához, és fűzze hozzá a címkét a gépekhez, amelyhez kódot kell aláírni.

Az Aláírás-érvényesítés szolgáltatás `Protect-GuestConfigurationPackage` használatához futtassa a parancsmabkal a csomag közzététel előtti aláírásához. Ehhez a parancsmaghoz "Kódaláírás" tanúsítvány szükséges.

A `Protect-GuestConfigurationPackage` parancsmag paraméterei:

- **Elérési út**: A Vendég konfigurációs csomag teljes elérési útja.
- **PublicGpgKeyPath**: Nyilvános GPG kulcs elérési útja. Ez a paraméter csak akkor támogatott, ha a tartalom aláírása linuxos.

A Linux-gépekkel használható GPG-kulcsok létrehozásához jó hivatkozást biztosít a GitHubon található cikk, [amely egy új GPG-kulcsot hoz létre.](https://help.github.com/en/articles/generating-a-new-gpg-key)

A GuestConfiguration ügynök elvárja, hogy a `/usr/local/share/ca-certificates/extra` tanúsítvány nyilvános kulcsa linuxos gépeken jelen legyen az elérési úton. Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, telepítse a tanúsítvány nyilvános kulcsát a számítógépre az egyéni házirend alkalmazása előtt. Ez a folyamat a virtuális gépen belüli bármilyen technikával, vagy az Azure Policy használatával végezhető el. Itt egy példasablon [található.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)
A Key Vault hozzáférési szabályzatának lehetővé kell tennie, hogy a számítási erőforrás-szolgáltató a központi telepítés során hozzáférjen a tanúsítványokhoz. A részletes lépéseket a [Key Vault beállítása virtuális gépekhez az Azure Resource Managerben című témakörben található.](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)

A tartalom közzététele után csatoljon egy `GuestConfigPolicyCertificateValidation` nevet `enabled` és értéket képviselő címkét minden olyan virtuális géphez, ahol kódaláírásra van szükség. Tekintse meg a [címke mintákat,](../samples/built-in-policies.md#tags) hogyan címkéket lehet szállítani az Azure Policy használatával. Ha ez a címke a helyén van, `New-GuestConfigurationPolicy` a parancsmag használatával létrehozott házirend-definíció lehetővé teszi a követelményt a Vendég konfigurációbővítményen keresztül.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Vendégkonfigurációs házirend-hozzárendelések hibaelhárítása (előzetes verzió)

Egy eszköz előzetes verzióban érhető el az Azure Policy Vendégkonfiguráció-hozzárendelések hibaelhárításához. Az eszköz előzetes verzióban érhető el, és a PowerShell-galériában [vendégkonfigurációs hibaelhárítóként](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/)lett közzétéve.

Az eszköz parancsmagjairól a PowerShell Súgó parancsával további információt kaphat a beépített útmutatás megjelenítéséhez. Mivel az eszköz egyre gyakori frissítéseket, ez a legjobb módja annak, hogy a legfrissebb információkat.

## <a name="next-steps"></a>További lépések

- További információ a vendégkonfigurációval rendelkező virtuális gépek [naplózásáról.](../concepts/guest-configuration.md)
- Ismerje meg, hogyan hozhat [létre programozott házirendeket.](programmatically-create.md)
- További információ a [megfelelőségi adatok beszedéséről.](get-compliance-data.md)
