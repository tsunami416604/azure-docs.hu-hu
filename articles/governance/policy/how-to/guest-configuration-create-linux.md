---
title: Vendégkonfigurációs szabályzatok létrehozása Linux rendszeren
description: Megtudhatja, hogyan hozhat létre Azure Policy vendég-konfigurációs házirendet Linux rendszerhez.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7510cf378bc7e2d999de122be27662a7ccf0ba92
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717540"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Vendégkonfigurációs szabályzatok létrehozása Linux rendszeren

Az egyéni szabályzatok létrehozása előtt olvassa el a [Azure Policy vendég konfigurációjának](../concepts/guest-configuration.md)áttekintését ismertető cikket.
 
A Windows rendszerhez készült vendég-konfigurációs szabályzatok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerhez készült vendég-konfigurációs szabályzatok](./guest-configuration-create.md) létrehozásának oldalát.

A Linux naplózásakor a Vendégkonfiguráció a [Chef InSpecet](https://www.inspec.io/) használja. Az InSpec-profil határozza meg a gép kívánt állapotát. Ha a konfiguráció kiértékelése meghiúsul, a rendszer elindítja a **auditIfNotExists** , és a gép **nem megfelelőnek**minősül.

[Azure Policy vendég konfiguráció](../concepts/guest-configuration.md) csak a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások szervizelése még nem érhető el.

A következő műveletek végrehajtásával hozhatja létre saját konfigurációját egy Azure-beli vagy nem Azure-beli gép állapotának ellenőrzéséhez.

> [!IMPORTANT]
> A vendég-konfigurációval rendelkező egyéni házirendek előzetes verziójú funkciók.
>
> A naplózás Azure-beli virtuális gépeken történő végrehajtásához szükség van a Vendégkonfiguráció bővítményre. Ha a bővítményt a Linux rendszerű gépek skáláján szeretné üzembe helyezni, rendelje hozzá a következő szabályzat-definíciót: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

A vendég konfigurációs modul automatizálja az egyéni tartalom létrehozásának folyamatát, többek között a következőket:

- Vendég konfigurációs tartalom (. zip) létrehozása
- Az összetevő automatizált tesztelése
- Házirend-definíció létrehozása
- A szabályzat közzététele

A modul Windows, macOS vagy Linux rendszerű gépen telepíthető, amely a PowerShell 6,2-as vagy újabb verzióját futtatja helyileg, vagy [Azure Cloud Shell](https://shell.azure.com)vagy a [Azure PowerShell Core Docker-rendszerkép](https://hub.docker.com/r/azuresdk/azure-powershell-core)használatával.

> [!NOTE]
> A konfigurációk fordítása Linux rendszeren nem támogatott.

### <a name="base-requirements"></a>Alapszintű követelmények

Azok az operációs rendszerek, amelyeken telepítve van a modul:

- Linux
- macOS
- Windows

> [!NOTE]
> A "test-GuestConfigurationPackage" parancsmaghoz az OpenSSL 1,0-es verziója szükséges. Ez hibát okoz az OpenSSL 1,1-es vagy újabb verziójával rendelkező környezetekben.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Vendég konfigurációs összetevők és szabályzat Linux rendszerhez

Még Linux-környezetekben is a vendég konfigurációja a kívánt állapot konfigurációját használja nyelvi absztrakcióként. A megvalósítás natív kódból (C++) alapul, így nincs szükség a PowerShell betöltésére. Ehhez azonban szükség van egy konfigurációs MOF-re, amely leírja a környezet részleteit.
A DSC burkolóként viselkedik a művelet végrehajtásához, a paraméterek meghatározásához, valamint a szolgáltatáshoz való visszatéréshez. A DSC kis ismerete szükséges, ha egyéni inspec-tartalommal dolgozik.

#### <a name="configuration-requirements"></a>Konfigurációs követelmények

Az egyéni konfiguráció nevének mindenütt konzisztensnek kell lennie. A Content csomag. zip fájljának nevét, a MOF-fájlban található konfiguráció nevét, valamint a Azure Resource Manager sablonban (ARM-sablon) lévő vendég-hozzárendelés nevét meg kell egyeznie.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Egyéni vendég konfigurációs konfiguráció Linuxon

A Linuxon a vendég konfigurációja az `ChefInSpecResource` erőforrás használatával biztosítja a motor számára az [inspec-profil](https://www.inspec.io/docs/reference/profiles/)nevét. A **név** az egyetlen szükséges erőforrás-tulajdonság. Hozzon létre egy YaML-fájlt és egy Ruby-parancsfájlt az alább részletezett módon.

Először hozza létre az inspec által használt YaML-fájlt. A fájl alapvető információkat biztosít a környezetről. Alább látható egy példa:

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

Mentse ezt a fájlt a `inspec.yml` `linux-path` Project-címtár nevű mappájába.

Ezután hozza létre a Ruby-fájlt a számítógép naplózásához használt inspec nyelvi absztrakcióval.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Mentse ezt a fájlt `linux-path.rb` egy nevű új mappába `controls` a `linux-path` címtárban.

Végül hozzon létre egy konfigurációt, importálja a **PSDesiredStateConfiguration** erőforrás-modult, és fordítsa le a konfigurációt.

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

Mentse ezt a fájlt `config.ps1` a Project mappában található néven. Futtassa a PowerShellben a terminálon történő végrehajtással `./config.ps1` . A rendszer létrehoz egy új MOF-fájlt.

A `Node AuditFilePathExists` parancs nem szükséges technikailag, de az alapértelmezett helyett egy nevű fájlt hoz létre `AuditFilePathExists.mof` `localhost.mof` . Ha a. MOF-fájlnevet követi, a konfiguráció megkönnyíti a sok fájl rendszerezését nagy léptékű működés esetén.

Most már az alábbiak szerint kell lennie egy projekt struktúrájának:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

A támogató fájlokat össze kell csomagolni. A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához.

A `New-GuestConfigurationPackage` parancsmag létrehozza a csomagot. A parancsmag paraméterei `New-GuestConfigurationPackage` Linux-tartalom létrehozásakor:

- **Name**: vendég konfigurációs csomag neve.
- **Konfiguráció**: a lefordított konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: kimeneti mappa elérési útja. Ezt a paramétert nem kötelező megadni. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.
- **ChefProfilePath**: az inspec-profil teljes elérési útja. Ez a paraméter csak akkor támogatott, ha tartalmat hoz létre a Linux rendszerű naplózáshoz.

A következő parancs futtatásával hozzon létre egy csomagot az előző lépésben megadott konfiguráció használatával:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

A konfigurációs csomag létrehozása után, de az Azure-ba való közzététel előtt tesztelheti a csomagot a munkaállomás vagy CI/CD-környezetből. A GuestConfiguration parancsmag `Test-GuestConfigurationPackage` ugyanazt az ügynököt tartalmazza a fejlesztői környezetben, mint amelyet az Azure-gépeken belül használ. Ezzel a megoldással helyileg is elvégezheti az integrációs tesztelést, mielőtt kiadná a számlázott felhőalapú környezeteket.

Mivel az ügynök ténylegesen kiértékeli a helyi környezetet, a legtöbb esetben a test-parancsmagot ugyanarra az operációsrendszer-platformra kell futtatnia, mint a naplózást.

A parancsmag paraméterei `Test-GuestConfigurationPackage` :

- **Név**: a vendég konfigurációs szabályzatának neve.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.

Futtassa a következő parancsot az előző lépésben létrehozott csomag teszteléséhez:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

A parancsmag a PowerShell-folyamatból is támogatja a bemenetet. A parancsmag kimenetének átadása `New-GuestConfigurationPackage` a `Test-GuestConfigurationPackage` parancsmagnak.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

A következő lépés a fájl közzététele a blob Storage-ban. Az alábbi szkript a feladat automatizálásához használható függvényt tartalmaz. A függvényben használt parancsokhoz a `publish` `Az.Storage` modul szükséges.

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
Miután létrehozta és feltöltötte a vendég konfigurációhoz tartozó egyéni házirend-csomagot, hozza létre a vendég-konfigurációs házirend definícióját. A `New-GuestConfigurationPolicy` parancsmag egy egyéni házirend-csomagot vesz fel, és létrehoz egy házirend-definíciót.

A parancsmag paraméterei `New-GuestConfigurationPolicy` :

- **ContentUri**: nyilvános http (s) URI a vendég konfigurációs tartalomkezelő csomaghoz.
- **DisplayName**: házirend megjelenítendő neve.
- **Leírás**: szabályzat leírása.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Verzió**: szabályzat verziója.
- **Elérési út**: a célhely elérési útja, ahol a szabályzat-definíciók létrejönnek
- **Platform**: cél platform (Windows/Linux) a vendég konfigurációs házirendhez és a tartalmi csomaghoz.
- A **címke** egy vagy több címkét rendel hozzá a szabályzat-definícióhoz
- **Kategória** – a szabályzat-definícióban a kategória metaadatainak mezőjének beállítása

A következő példa egy egyéni házirend-csomagból egy megadott elérési úton hozza létre a házirend-definíciókat:

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

A következő fájlokat hozza létre `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsbekapcsolva**
- **deployIfNotExists.jsbekapcsolva**
- **Initiative.jsbekapcsolva**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a házirend-fájlok kezdeményezésének megjelenítendő nevét és elérési útját tartalmazza.

Végül tegye közzé a szabályzat-definíciókat a `Publish-GuestConfigurationPolicy` parancsmag használatával. A parancsmag csak a **path** paraméterrel rendelkezik, amely a által létrehozott JSON-fájlok helyére mutat `New-GuestConfigurationPolicy` .

A közzétételi parancs futtatásához hozzá kell férnie a szabályzatok létrehozásához az Azure-ban. A konkrét engedélyezési követelmények dokumentálva vannak a [Azure Policy áttekintés](../overview.md) oldalon. A legjobb beépített szerepkör az erőforrás- **házirend közreműködője**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 A `Publish-GuestConfigurationPolicy` parancsmag fogadja az elérési utat a PowerShell-folyamatból. Ez a funkció azt jelenti, hogy létrehozhatja a házirend-fájlokat, és közzéteheti őket egyetlen vezetékes parancsban.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Az Azure-ban létrehozott szabályzattal az utolsó lépés a kezdeményezés kiosztása. Lásd: a kezdeményezés társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

> [!IMPORTANT]
> A _AuditIfNotExists_ -és _DeployIfNotExists_ -házirendeket egyesítő kezdeményezéssel **mindig** hozzá kell rendelni a vendég-konfigurációs házirendeket. Ha csak a _AuditIfNotExists_ szabályzat van hozzárendelve, az előfeltételek nincsenek telepítve, és a házirend mindig azt mutatja, hogy a "0" kiszolgálók megfelelőek.

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

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Paraméterek használata az egyéni vendég-konfigurációs házirendekben

A vendég konfiguráció futási időben támogatja a konfiguráció felülírási tulajdonságait. Ez a funkció azt jelenti, hogy a csomagban lévő MOF-fájlban lévő értékeket nem kell statikusnak tekinteni. A felülbírálási értékek a Azure Policyon keresztül érhetők el, és nem befolyásolják a konfigurációk létrehozási vagy fordítási módját.

Az inspec paraméterrel jellemzően a paramétereket a rendszer a futtatókörnyezetben vagy az attribútumokat használó kódokként kezeli. A vendég konfigurációja elhomályosítja ezt a folyamatot, így a rendszer a szabályzat hozzárendelésekor megadhatja a bemenetet. A rendszer automatikusan létrehoz egy attribútum-fájlt a gépen. Nem kell létrehoznia és felvennie egy fájlt a projektben. A Linux audit projekthez két lépéssel adhat hozzá paramétereket.

Adja meg azt a Ruby-fájlban található bemenetet, amelyen a számítógépen naplózni szeretné a naplót. Alább látható egy példa.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

A parancsmagok `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` tartalmazzák a **paraméter**nevű paramétert. Ez a paraméter egy szórótábla tartalmaz, amely tartalmazza az egyes paraméterek összes részletét, és automatikusan létrehozza az egyes Azure Policy-definíciók létrehozásához használt fájlok összes szükséges részét.

Az alábbi példa egy házirend-definíciót hoz létre egy fájl elérési útjának naplózásához, ahol a felhasználó a házirend-hozzárendelés időpontjában megadja az elérési utat.

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
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

Linux-házirendek esetében adja meg a konfiguráció **AttributesYmlContent** tulajdonságát, és szükség szerint írja felül az értékeket. A vendég konfigurációs ügynök automatikusan létrehozza a YAML-fájlt, amelyet az inspec az attribútumok tárolására használ. Lásd az alábbi példát.

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

## <a name="policy-lifecycle"></a>Szabályzat életciklusa

A házirend-definíció frissítésének kiadásához két, figyelmet igénylő mező szükséges.

- **Verzió**: a parancsmag futtatásakor meg `New-GuestConfigurationPolicy` kell adnia a jelenleg közzétett verziónál nagyobb verziószámot. A tulajdonság frissíti a vendég konfiguráció-hozzárendelés verzióját, hogy az ügynök felismeri a frissített csomagot.
- **contentHash**: ezt a tulajdonságot a parancsmag automatikusan frissíti `New-GuestConfigurationPolicy` . Ez a csomag által létrehozott kivonatoló érték `New-GuestConfigurationPackage` . A tulajdonságnak megfelelőnek kell lennie a `.zip` közzétett fájlhoz. Ha csak a **contentUri** tulajdonság frissül, a bővítmény nem fogadja el a csomag tartalmát.

Egy frissített csomag kiadásának legegyszerűbb módja, ha megismétli a jelen cikkben ismertetett folyamatot, és megadja a verziószámot. Ez a folyamat garantálja az összes tulajdonság megfelelő frissítését.


### <a name="filtering-guest-configuration-policies-using-tags"></a>Vendég konfigurációs szabályzatok szűrése címkék használatával

A parancsmagok által a vendég konfigurációs modulban létrehozott házirendek opcionálisan tartalmazhatnak szűrőket a címkékhez. A **-tag** paraméter a `New-GuestConfigurationPolicy` támogatja az egyéni címkéket tartalmazó szórótáblában tömbjét. A címkék hozzá lesznek adva a `If` szabályzat-definíció szakaszához, és nem módosíthatók házirend-hozzárendeléssel.

Az alábbi példa egy olyan szabályzat-definíció kódrészletét adja meg, amely a címkék szűrésére szolgál.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Nem kötelező: a vendég konfigurációs csomagjainak aláírása

A vendég konfigurációja egyéni házirendek SHA256 kivonatot használnak a házirend-csomag érvényesítéséhez.
Igény szerint az ügyfelek is használhatnak tanúsítványokat a csomagok aláírására és a vendég konfigurációs bővítmény kényszerítésére, hogy csak az aláírt tartalmat engedélyezzék.

Ennek a forgatókönyvnek az engedélyezéséhez két lépést kell végrehajtania. Futtassa a parancsmagot a tartalomindex aláírásához, és fűzze hozzá a címkét azokhoz a gépekhez, amelyeknek az aláírásához kód szükséges.

Az aláírás-ellenőrzési funkció használatához futtassa a `Protect-GuestConfigurationPackage` parancsmagot a csomag aláírásához a közzététel előtt. Ehhez a parancsmaghoz "kód aláírása" tanúsítvány szükséges.

A parancsmag paraméterei `Protect-GuestConfigurationPackage` :

- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.
- **PublicGpgKeyPath**: nyilvános GPG-kulcs elérési útja. Ez a paraméter csak a Linux-tartalmak aláírása esetén támogatott.

A Linux rendszerű gépekhez használható GPG-kulcsok létrehozásának jó referenciája a GitHubon található cikk, [új GPG-kulcs](https://help.github.com/en/articles/generating-a-new-gpg-key)létrehozása.

A GuestConfiguration-ügynök elvárja, hogy a tanúsítvány nyilvános kulcsa jelen legyen a Linux rendszerű gépek elérési útjában `/usr/local/share/ca-certificates/extra` . Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, az egyéni házirend alkalmazása előtt telepítse a tanúsítvány nyilvános kulcsát a gépre. Ez a folyamat a virtuális gépen belüli bármely technikával vagy Azure Policy használatával végezhető el. [Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)található egy példa erre a sablonra.
Az Key Vault hozzáférési szabályzatnak lehetővé kell tennie a számítási erőforrás-szolgáltató számára a tanúsítványok elérését a központi telepítések során. A részletes lépésekért lásd: [Key Vault beállítása virtuális gépekhez a Azure Resource Managerban](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

A tartalom közzététele után fűzze hozzá a nevet és az értéket tartalmazó címkét az `GuestConfigPolicyCertificateValidation` `enabled` összes olyan virtuális géphez, amelynél szükség van a kód aláírására. Tekintse meg azokat a [mintákat](../samples/built-in-policies.md#tags) , amelyekkel a címkék a Azure Policy használatával méretezhetők. A címke betartása után a parancsmag használatával generált szabályzat-definíció `New-GuestConfigurationPolicy` engedélyezi a követelményt a vendég konfigurációs bővítményen keresztül.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>A vendég-konfigurációs házirend hozzárendeléseinek hibaelhárítása (előzetes verzió)

Egy eszköz előzetes verzióban érhető el, amely segítséget nyújt az Azure Policy vendég konfigurációs hozzárendeléseinek hibaelhárításában. Az eszköz előzetes verzióban érhető el, és közzé lett téve a PowerShell-galéria mint modul neve [vendég konfigurációs hibakereső](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Az eszköz parancsmagokkal kapcsolatos további információkért használja a PowerShell Get-Help parancsát a beépített útmutatás megjelenítéséhez. Mivel az eszköz gyakori frissítéseket kap, ez a legjobb módszer a legfrissebb információk beszerzésére.

## <a name="next-steps"></a>További lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](./programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](./get-compliance-data.md).
