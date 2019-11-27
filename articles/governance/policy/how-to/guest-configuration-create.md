---
title: Vendég-konfigurációs szabályzatok létrehozása
description: Megtudhatja, hogyan hozhat létre Azure Policy vendég-konfigurációs szabályzatot Windows vagy Linux rendszerű virtuális gépekhez a Azure PowerShell használatával.
ms.date: 11/21/2019
ms.topic: conceptual
ms.openlocfilehash: 2e653d07e783425afdcd71f9d58e3569692faaf9
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407056"
---
# <a name="how-to-create-guest-configuration-policies"></a>Vendég-konfigurációs szabályzatok létrehozása

A vendég konfigurációja a [kívánt állapot-konfigurációs](/powershell/scripting/dsc/overview/overview) (DSC) erőforrás-modult használja az Azure-gépek naplózási konfigurációjának létrehozásához. A DSC-konfiguráció azt a feltételt határozza meg, amelyet a gépen be kell állítani. Ha a konfiguráció kiértékelése meghiúsul, a rendszer elindítja a **auditIfNotExists** , és a gép **nem megfelelőnek**minősül.

[Azure Policy vendég konfiguráció](../concepts/guest-configuration.md) csak a gépeken belüli beállítások naplózására használható. A gépeken belüli beállítások szervizelése még nem érhető el.

Az alábbi műveletek végrehajtásával hozhatja létre saját konfigurációját egy Azure-gép állapotának ellenőrzéséhez.

> [!IMPORTANT]
> A vendég-konfigurációval rendelkező egyéni házirendek előzetes verziójú funkciók.

## <a name="add-the-guestconfiguration-resource-module"></a>A GuestConfiguration erőforrás-modul hozzáadása

A vendég-konfigurációs szabályzat létrehozásához hozzá kell adni az erőforrás-modult. Ez az erőforrás-modul helyileg telepített PowerShell-lel, [Azure Cloud Shell](https://shell.azure.com)vagy az [Azure PowerShell Core Docker-lemezképpel](https://hub.docker.com/r/azuresdk/azure-powershell-core)használható.

### <a name="base-requirements"></a>Alapszintű követelmények

A vendég konfigurációs erőforrás-modulhoz a következő szoftverek szükségesek:

- PowerShell. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>A modul telepítése

A vendég konfigurációja a **GuestConfiguration** erőforrás-modult használja a DSC-konfigurációk létrehozásához és a Azure Policy közzétételéhez:

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

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Egyéni vendég konfigurációs konfiguráció és erőforrások létrehozása

A vendég konfigurációhoz tartozó egyéni szabályzat létrehozásának első lépése a DSC-konfiguráció létrehozása. A DSC-fogalmak és a terminológia áttekintését lásd: a [POWERSHELL DSC áttekintése](/powershell/scripting/dsc/overview/overview).

Ha a konfigurációban csak a vendég konfigurációs ügynök telepítésével rendelkező erőforrások szükségesek, akkor csak egy konfigurációs MOF-fájlt kell létrehoznia. Ha további parancsfájlt kell futtatnia, létre kell hoznia egy egyéni erőforrás-modult.

### <a name="requirements-for-guest-configuration-custom-resources"></a>A vendég-konfiguráció egyéni erőforrásaira vonatkozó követelmények

Amikor a vendég konfigurációja naplóz egy gépet, először a `Test-TargetResource` futtatja annak megállapításához, hogy a megfelelő állapotban van-e. A függvény által visszaadott logikai érték határozza meg, hogy a vendég-hozzárendelés Azure Resource Manager állapotának megfelelőnek vagy nem megfelelőnek kell lennie. Ha a logikai érték `$false` a konfigurációban lévő összes erőforráshoz, akkor a szolgáltató `Get-TargetResource`fog futni. Ha a logikai érték `$true`, akkor `Get-TargetResource` nincs meghívva.

A `Get-TargetResource` függvény speciális követelményeket tartalmaz a Windows kívánt állapotának konfigurálásához nem szükséges vendég-konfigurációhoz.

- A visszaadott szórótábla tartalmaznia kell **egy nevű**tulajdonságot.
- A Reason tulajdonságnak tömbnek kell lennie.
- A tömb minden elemének olyan szórótábla kell lennie, amelynek **kóddal** és **kifejezéssel**ellátott kulcsokkal kell rendelkeznie.

A szolgáltatás az okok tulajdonságát arra használja, hogy egységesítse, hogyan jelennek meg az információk, amikor egy gép nem felel meg az előírásoknak. Az egyes elemek oka az lehet, hogy az erőforrás nem megfelelő. A tulajdonság egy tömb, mert egy erőforrás több okból nem felel meg a megfelelőségnek.

A szolgáltatás a tulajdonságok **kódját** és a **kifejezést** is elvárta. Egyéni erőforrás létrehozásakor állítsa be azt a szöveget (jellemzően StdOut), amelyet az erőforrás nem felel meg a **kifejezés**értékének. A **kód** meghatározott formázási követelményekkel rendelkezik, így a jelentéskészítés egyértelműen megjeleníti a naplózás végrehajtásához használt erőforrás adatait. Ez a megoldás a vendég konfigurációját bővíthetővé teszi. Bármely parancs futtatható a gép naplózásához, ha a kimenet rögzíthető, és karakterlánc-értékként lesz visszaadva a **kifejezés** tulajdonsághoz.

- **Code** (string) (karakterlánc): az erőforrás neve, ismétlődő, majd egy rövid név, amely nem tartalmazhat szóközt azonosítóként az ok miatt. Ez a három érték csak kettősponttal tagolható szóközök nélkül.
  - Példa `registry:registry:keynotpresent`
- **Kifejezés** (karakterlánc): ember által olvasható szöveg, amely elmagyarázza, hogy a beállítás miért nem megfelelő.
  - Példa `The registry key $key is not present on the machine.`

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

#### <a name="scaffolding-a-guest-configuration-project"></a>Vendég konfigurációs projekt állványzata

Azoknak a fejlesztőknek, akik szeretnék felgyorsítani az első lépések és a mintakód használatának folyamatát, egy **vendég konfigurációs projekt** nevű közösségi projekt létezik sablonként a [vakolat](https://github.com/powershell/plaster) PowerShell-modulhoz. Ezzel az eszközzel a projekt beépíthető, beleértve a munkakonfigurációt és a minta erőforrást, valamint a projekt ellenőrzéséhez szükséges, a tevékenység ellenőrzésére szolgáló feldolgozatlan [teszteket](https://github.com/pester/pester) . A sablon a Visual Studio Code feladatait is tartalmazza, amelyekkel automatizálható a vendég-konfigurációs csomag kiépítése és érvényesítése. További információ: GitHub Project [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

### <a name="custom-guest-configuration-configuration-on-linux"></a>Egyéni vendég konfigurációs konfiguráció Linuxon

A Linuxon futó vendég-konfiguráció DSC-konfigurációja a `ChefInSpecResource` erőforrást használja, hogy a motor a [Chef inspec](https://www.chef.io/inspec/) definíciójának nevét adja meg. A **név** az egyetlen szükséges erőforrás-tulajdonság.

Az alábbi példa létrehoz egy alapkonfigurációt, importálja a **GuestConfiguration** erőforrás-modult **, és**a `ChefInSpecResource` erőforrást használja az inspec definíciójának neveként a **Linux-patch-alapterv**értékre:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

További információt a [konfiguráció írása, fordítása és alkalmazása](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)című témakörben talál.

### <a name="custom-guest-configuration-configuration-on-windows"></a>Egyéni vendég konfigurációs konfiguráció Windows rendszeren

A Azure Policy vendég konfigurációjának DSC-konfigurációját csak a vendég konfigurációs ügynök használja, nem ütközik a Windows PowerShell kívánt állapotának konfigurációjával.

A következő példa létrehoz egy **AuditBitLocker**nevű konfigurációt, importálja a **GuestConfiguration** erőforrás-modult, és a `Service` erőforrást használja a futó szolgáltatás naplózásához:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

További információt a [konfiguráció írása, fordítása és alkalmazása](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)című témakörben talál.

## <a name="create-guest-configuration-custom-policy-package"></a>Vendég-konfiguráció egyéni házirend-csomagjának létrehozása

A MOF lefordítása után a támogató fájlokat össze kell csomagolni. A kitöltött csomagot a vendég konfigurációja használja a Azure Policy definíciók létrehozásához. A csomag a következőkből áll:

- A lefordított DSC-konfiguráció MOF-ként
- Modulok mappa
  - GuestConfiguration modul
  - DscNativeResources modul
  - Linux Egy, a Chef Inspect definícióval és további tartalommal rendelkező mappa
  - Windows A nem beépített DSC-erőforrás-modulok

A `New-GuestConfigurationPackage` parancsmag létrehozza a csomagot. Egyéni csomag létrehozásához a következő formátumot használja a rendszer:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

A `New-GuestConfigurationPackage` parancsmag paraméterei:

- **Name**: vendég konfigurációs csomag neve.
- **Konfiguráció**: LEfordított DSC-konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: kimeneti mappa elérési útja. Ez a paraméter nem kötelező. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.
- **ChefProfilePath**: az inspec-profil teljes elérési útja. Ez a paraméter csak akkor támogatott, ha tartalmat hoz létre a Linux rendszerű naplózáshoz.

A befejezett csomagot a felügyelt virtuális gépek által elérhető helyen kell tárolni. Ilyenek például a GitHub-adattárak, az Azure-Tárházak vagy az Azure Storage. Ha nem szeretné, hogy a csomag nyilvános legyen, az URL-címben egy [sas-tokent](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) is hozzáadhat.
A magánhálózati számítógépekhez [szolgáltatási végpontot](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) is alkalmazhat, bár ez a konfiguráció csak a csomag elérésére és a szolgáltatással való kommunikációra vonatkozik.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>A titkok használata a vendég konfigurációs csomagjaiban

Azure Policy vendég konfigurációban a futtatáskor használt titkok kezelésének optimális módja, ha a Azure Key Vault tárolja őket. Ez a kialakítás az egyéni DSC-erőforrásokon belül valósítható meg.

1. Először hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az Azure-ban.

   Az identitást a gépek használják a Key Vaultban tárolt titkos kódok elérésére. A részletes lépésekért lásd: [felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása és törlése Azure PowerShell használatával](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

1. Hozzon létre egy Key Vault példányt.

   Részletes lépések: [titkos PowerShell beállítása és beolvasása](../../../key-vault/quick-create-powershell.md).
   Rendeljen engedélyeket a példányhoz, hogy a felhasználó által hozzárendelt identitás hozzáférést biztosítson a Key Vaultban tárolt titkos kulcsokhoz. Részletes lépések: [Secret-.net beállítása és beolvasása](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

1. Rendelje hozzá a felhasználó által hozzárendelt identitást a számítógéphez.

   A részletes lépésekért lásd: [felügyelt identitások konfigurálása](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity)Azure-beli virtuális gépeken a PowerShell használatával.
   A skálán ezt az identitást a Azure Resource Manager segítségével Azure Policy használatával rendelheti hozzá. A részletes lépésekért lásd: [felügyelt identitások konfigurálása](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm)Azure-beli virtuális gépeken sablon használatával.

1. Végül az egyéni erőforráson belül a fentiekben generált ügyfél-azonosítót használva férhet hozzá Key Vault a számítógépről elérhető token használatával.

   A Key Vault példányhoz tartozó `client_id` és URL-cím [tulajdonságokként](/powershell/scripting/dsc/resources/authoringresourcemof#creating-the-mof-schema) adható át az erőforrásnak, így az erőforrást nem kell több környezet esetében frissíteni, vagy ha az értékeket módosítani kell.

Az alábbi mintakód egy egyéni erőforrásban használható a titkos kulcsok Key Vault felhasználó által hozzárendelt identitással való lekéréséhez. A kérelemből Key Vault egyszerű szövegként visszaadott érték. Ajánlott eljárásként tárolja azt egy hitelesítőadat-objektumon belül.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Vendég konfigurációs csomag tesztelése

A konfigurációs csomag létrehozása után, de az Azure-ba való közzététel előtt tesztelheti a csomag funkcióit a munkaállomás vagy a CI/CD-környezet használatával. A GuestConfiguration modul olyan parancsmagot tartalmaz, `Test-GuestConfigurationPackage` amely az Azure-gépeken belül használt fejlesztési környezetben ugyanazt az ügynököt tölti be. Ezzel a megoldással helyileg is elvégezheti az integrációs tesztelést, mielőtt kiadná a kiszámlázott tesztelési/QA/éles környezeteket.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

A `Test-GuestConfigurationPackage` parancsmag paraméterei:

- **Név**: a vendég konfigurációs szabályzatának neve.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Elérési út**: a vendég konfigurációs csomag teljes elérési útja.

A parancsmag a PowerShell-folyamatból is támogatja a bemenetet. `New-GuestConfigurationPackage` parancsmag kimenetének átadása a `Test-GuestConfigurationPackage` parancsmagnak.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

A paraméterekkel való teszteléssel kapcsolatos további információkért lásd az alábbi szakaszt az [Egyéni vendég konfigurációs szabályzatokban található paraméterek használatával](#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>A Azure Policy-definíció és-kezdeményezés telepítési fájljainak létrehozása

Miután létrehozott egy egyéni házirend-csomagot, és a gépek által elérhető helyre lett feltöltve, hozza létre a vendég-konfigurációs házirend definícióját a Azure Policyhoz. Az `New-GuestConfigurationPolicy` parancsmag nyilvánosan elérhető vendég-konfiguráció egyéni házirend-csomagot hoz létre, és létrehoz egy **auditIfNotExists** és egy **deployIfNotExists** szabályzat-definíciót. Egy házirend-kezdeményezési definíció is létrejön, amely magában foglalja mindkét házirend-definíciót is.

A következő példa egy megadott elérési úton létrehoz egy házirend-és kezdeményezési definíciót egy vendég konfigurációja egyéni Windows-csomagból, és megadja a nevet, a leírást és a verziót:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

A `New-GuestConfigurationPolicy` parancsmag paraméterei:

- **ContentUri**: nyilvános http (s) URI a vendég konfigurációs tartalomkezelő csomaghoz.
- **DisplayName**: házirend megjelenítendő neve.
- **Leírás**: szabályzat leírása.
- **Paraméter**: szórótábla formátumban megadott házirend-paraméterek.
- **Verzió**: szabályzat verziója.
- **Elérési út**: a célhely elérési útja, ahol a szabályzat-definíciók létrejönnek
- **Platform**: cél platform (Windows/Linux) a vendég konfigurációs házirendhez és a tartalmi csomaghoz.

A `New-GuestConfigurationPolicy`a következő fájlokat hozza létre:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a házirend-fájlok kezdeményezésének megjelenítendő nevét és elérési útját tartalmazza.

Ha ezt a parancsot egy egyéni házirend-projekt beépítéséhez szeretné használni, módosíthatja ezeket a fájlokat. Ilyen például a "If" szakasz módosítása annak kiértékeléséhez, hogy van-e egy adott címke a gépekhez. A házirendek létrehozásával kapcsolatos részletes információkat a [szabályzatok programozott létrehozása](./programmatically-create.md)című témakörben talál.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Paraméterek használata az egyéni vendég-konfigurációs házirendekben

A vendég konfiguráció futási időben támogatja a konfiguráció felülírási tulajdonságait. Ez a funkció azt jelenti, hogy a csomagban lévő MOF-fájlban lévő értékeket nem kell statikusnak tekinteni. A felülbírálási értékek a Azure Policyon keresztül érhetők el, és nem befolyásolják a konfigurációk létrehozási vagy fordítási módját.

A `New-GuestConfigurationPolicy` és `Test-GuestConfigurationPolicyPackage` parancsmagok tartalmazzák a **Paraméterek**nevű paramétert. Ez a paraméter egy szórótábla-definíciót vesz fel, amely tartalmazza az egyes paraméterek összes részletét, és automatikusan létrehozza az egyes Azure Policy-definíciók létrehozásához használt fájlok összes szükséges részét.

A következő példa egy Azure Policyt hoz létre a szolgáltatás naplózásához, ahol a felhasználó a szabályzat-hozzárendelés időpontjában kiválasztja a szolgáltatások listáját.

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
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Linux-szabályzatok esetén a konfigurációban adja meg a **AttributesYmlContent** tulajdonságot, és ennek megfelelően írja felül az értékeket. A vendég konfigurációs ügynök automatikusan létrehozza a YaML-fájlt, amelyet az inspec az attribútumok tárolására használ. Lásd az alábbi példát.

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Minden további paraméterhez adjon hozzá egy szórótábla a tömbhöz. A házirend-fájlokban a vendég konfigurációs configurationName felvett tulajdonságokat fogja látni, amelyek az erőforrás típusát, nevét, tulajdonságát és értékét azonosítják.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Közzététel Azure Policy

A **GuestConfiguration** erőforrás-modul lehetővé teszik a szabályzat-definíciók és a kezdeményezési definíciók létrehozását az Azure-ban egy lépéssel az `Publish-GuestConfigurationPolicy` parancsmaggal.
A parancsmag csak a **path** paraméterrel rendelkezik, amely a `New-GuestConfigurationPolicy`által létrehozott három JSON-fájl helyére mutat.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

A `Publish-GuestConfigurationPolicy` parancsmag elfogadja a PowerShell-folyamat elérési útját. Ez a funkció azt jelenti, hogy létrehozhatja a házirend-fájlokat, és közzéteheti őket egyetlen vezetékes parancsban.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Az Azure-ban létrehozott házirend-és kezdeményezési definíciókkal az utolsó lépés a kezdeményezés kiosztása. Lásd: a kezdeményezés társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

> [!IMPORTANT]
> A _AuditIfNotExists_ -és _DeployIfNotExists_ -házirendeket egyesítő kezdeményezéssel **mindig** hozzá kell rendelni a vendég-konfigurációs házirendeket. Ha csak a _AuditIfNotExists_ szabályzat van hozzárendelve, az előfeltételek nincsenek telepítve, és a házirend mindig azt mutatja, hogy a "0" kiszolgálók megfelelőek.

## <a name="policy-lifecycle"></a>Szabályzat életciklusa

Miután közzétett egy egyéni Azure Policy az egyéni tartalomkezelő csomag használatával, két mezőt kell frissíteni, ha új kiadást szeretne közzétenni.

- **Verzió**: az `New-GuestConfigurationPolicy` parancsmag futtatásakor meg kell adnia egy verziószámot, amely nagyobb a jelenleg közzétettnél. A tulajdonság frissíti a vendég konfiguráció-hozzárendelés verzióját az új házirend-fájlban, így a bővítmény felismeri, hogy a csomag frissítve lett.
- **contentHash**: ezt a tulajdonságot a `New-GuestConfigurationPolicy` parancsmag automatikusan frissíti. Ez a `New-GuestConfigurationPackage`által létrehozott csomag kivonatának értéke. A tulajdonságnak megfelelőnek kell lennie a közzétett `.zip` fájlhoz. Ha csak a **contentUri** tulajdonság frissül, például abban az esetben, ha valaki manuálisan módosíthatja a házirend-definíciót a portálon, a bővítmény nem fogadja el a tartalmi csomagot.

Egy frissített csomag kiadásának legegyszerűbb módja, ha megismétli a jelen cikkben ismertetett folyamatot, és megadja a verziószámot. Ez a folyamat garantálja az összes tulajdonság megfelelő frissítését.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows Csoportházirend tartalom konvertálása Azure Policy vendég konfigurációra

A vendég konfigurációja a Windows rendszerű gépek naplózásakor a PowerShell desired State Configuration szintaxisának implementációja. A DSC-Közösség közzétette az exportált Csoportházirend-sablonok DSC formátumra való konvertálásának eszközét. Az eszköznek a fent ismertetett vendég konfigurációs parancsmagokkal együtt történő használatával átalakíthatja a Windows Csoportházirend tartalmát, és becsomagolhatja vagy közzéteheti a Azure Policy a naplózáshoz. További információ az eszköz használatáról [: a csoportházirend átalakítása a DSC-be](/powershell/scripting/dsc/quickstarts/gpo-quickstart)című cikk rövid útmutatója.
A tartalom konvertálása után a fenti lépéseket követve hozzon létre egy csomagot, és tegye közzé Azure Policyként, ugyanúgy, mint bármely DSC-tartalomhoz.

## <a name="optional-signing-guest-configuration-packages"></a>Nem kötelező: a vendég konfigurációs csomagjainak aláírása

A vendég konfigurációja egyéni szabályzatok alapértelmezés szerint a SHA256-kivonattal ellenőrzik, hogy a házirend-csomag nem módosult-e, amikor közzé lett téve, amikor a naplózott kiszolgáló beolvassa.
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
- **PrivateGpgKeyPath**: Private GPG-kulcs elérési útja. Ez a paraméter csak a Linux-tartalmak aláírása esetén támogatott.
- **PublicGpgKeyPath**: nyilvános GPG-kulcs elérési útja. Ez a paraméter csak a Linux-tartalmak aláírása esetén támogatott.

A GuestConfiguration-ügynök elvárja, hogy a tanúsítvány nyilvános kulcsa elérhető legyen a Windows rendszerű gépeken a "megbízható legfelső szintű hitelesítésszolgáltatók" szolgáltatásban, illetve a Linux rendszerű gépek elérési útján `/usr/local/share/ca-certificates/extra`. Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, az egyéni házirend alkalmazása előtt telepítse a tanúsítvány nyilvános kulcsát a gépre. Ez a folyamat a virtuális gépen belüli bármely technikával vagy Azure Policy használatával végezhető el. [Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)található egy példa erre a sablonra.
Az Key Vault hozzáférési szabályzatnak lehetővé kell tennie a számítási erőforrás-szolgáltató számára a tanúsítványok elérését a központi telepítések során. A részletes lépésekért lásd: [Key Vault beállítása virtuális gépekhez a Azure Resource Managerban](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Az alábbi példa a nyilvános kulcs exportálását írja elő egy aláíró tanúsítványból a gépre való importáláshoz.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

A Linux rendszerű gépekhez használható GPG-kulcsok létrehozásának jó referenciája a GitHubon található cikk, [új GPG-kulcs](https://help.github.com/en/articles/generating-a-new-gpg-key)létrehozása.

A tartalom közzététele után fűzze hozzá a `GuestConfigPolicyCertificateValidation` nevű címkét és a Value `enabled` értéket minden olyan virtuális géphez, amelynél szükség van a kód aláírására. Ezt a címkét a Azure Policy használatával lehet méretezni. Lásd: [címke alkalmazása és az alapértelmezett érték](../samples/apply-tag-default-value.md) minta. A címke betartása után az `New-GuestConfigurationPolicy` parancsmag használatával generált házirend-definíció engedélyezi a követelményt a vendég konfigurációs bővítményen keresztül.

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>ELŐNÉZET Vendég-konfigurációs szabályzatok hozzárendeléseinek hibaelhárítása

Egy eszköz előzetes verzióban érhető el, amely segítséget nyújt az Azure Policy vendég konfigurációs hozzárendeléseinek hibaelhárításában. Az eszköz előzetes verzióban érhető el, és közzé lett téve a PowerShell-galéria mint modul neve [vendég konfigurációs hibakereső](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Az eszköz parancsmagokkal kapcsolatos további információkért használja a PowerShell Get-Help parancsát a beépített útmutatás megjelenítéséhez. Mivel az eszköz gyakori frissítéseket kap, ez a legjobb módszer a legfrissebb információk beszerzésére.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](get-compliance-data.md).