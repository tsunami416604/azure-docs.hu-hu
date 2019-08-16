---
title: Vendég-konfigurációs szabályzatok létrehozása
description: Megtudhatja, hogyan hozhat létre Azure Policy vendég-konfigurációs szabályzatot Windows vagy Linux rendszerű virtuális gépekhez.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 131d6865c47a32bbefbfbd397a5f0f88dedc9c35
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543508"
---
# <a name="how-to-create-guest-configuration-policies"></a>Vendég-konfigurációs szabályzatok létrehozása

A vendég konfigurációja a [kívánt állapot-konfigurációs](/powershell/dsc) (DSC) erőforrás-modult használja az Azure-beli virtuális gépek naplózási konfigurációjának létrehozásához. A DSC-konfiguráció azt a feltételt határozza meg, amelyben a virtuális gépnek szerepelnie kell. Ha a konfiguráció kiértékelése nem sikerül, a rendszer elindítja a házirend hatásának naplózását, és a virtuális gép **nem megfelelőnek**minősül.

[Azure Policy vendég konfiguráció](/azure/governance/policy/concepts/guest-configuration) csak a virtuális gépeken belüli beállítások naplózására használható. A virtuális gépeken belüli beállítások szervizelése még nem érhető el.

Az alábbi műveletek végrehajtásával hozhatja létre saját konfigurációját egy Azure-beli virtuális gép állapotának ellenőrzéséhez.

> [!IMPORTANT]
> A vendég-konfigurációval rendelkező egyéni házirendek előzetes verziójú funkciók.

## <a name="add-the-guestconfiguration-resource-module"></a>A GuestConfiguration erőforrás-modul hozzáadása

A vendég-konfigurációs szabályzat létrehozásához hozzá kell adni az erőforrás-modult. Ez az erőforrás-modul helyileg telepített PowerShell-lel, [Azure Cloud Shell](https://shell.azure.com)vagy a [Azure PowerShell Docker-lemezképpel](https://hub.docker.com/rsdk-powershell/)használható.

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

## <a name="create-custom-guest-configuration-configuration"></a>Egyéni vendég konfiguráció konfigurációjának létrehozása

A vendég konfigurációhoz tartozó egyéni szabályzat létrehozásának első lépése a DSC-konfiguráció létrehozása. A DSC-fogalmak és a terminológia áttekintését lásd: a [POWERSHELL DSC áttekintése](/powershell/dsc/overview/overview).

### <a name="custom-guest-configuration-configuration-on-linux"></a>Egyéni vendég konfigurációs konfiguráció Linuxon

A Linuxon futó vendég-konfiguráció DSC-konfigurációja `ChefInSpecResource` az erőforrást használja, hogy a motor a [Chef inspec](https://www.chef.io/inspec/) definíciójának nevét adja meg. A **név** az egyetlen szükséges erőforrás-tulajdonság.

Az alábbi példa létrehoz egy alapkonfigurációt, importálja a **GuestConfiguration** erőforrás-modult, `ChefInSpecResource` és az erőforrást az inspec definíciójának neve alapján állítja be a **Linux-patch-alapterv**értékre:

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

További információt a [konfiguráció írása, fordítása és alkalmazása](/powershell/dsc/configurations/write-compile-apply-configuration)című témakörben talál.

### <a name="custom-guest-configuration-configuration-on-windows"></a>Egyéni vendég konfigurációs konfiguráció Windows rendszeren

A Azure Policy vendég konfigurációjának DSC-konfigurációját csak a vendég konfigurációs ügynök használja, nem ütközik a Windows PowerShell kívánt állapotának konfigurációjával.

A következő példa létrehoz egy **AuditBitLocker**nevű konfigurációt, importálja a **GuestConfiguration** erőforrás-modult `Service` , és az erőforrást egy futó szolgáltatás naplózására használja:

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

További információt a [konfiguráció írása, fordítása és alkalmazása](/powershell/dsc/configurations/write-compile-apply-configuration)című témakörben talál.

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

- **Név**: Vendég konfigurációs csomag neve.
- **Konfigurálás**: Lefordított DSC-konfigurációs dokumentum teljes elérési útja.
- **Elérési út**: Kimeneti mappa elérési útja Ez a paraméter nem kötelező. Ha nincs megadva, a csomag az aktuális könyvtárban jön létre.
- **ChefProfilePath**: Az inspec-profil teljes elérési útja. Ez a paraméter csak akkor támogatott, ha tartalmat hoz létre a Linux rendszerű naplózáshoz.

A befejezett csomagot a felügyelt virtuális gépek által elérhető helyen kell tárolni. Ilyenek például a GitHub-adattárak, az Azure-Tárházak vagy az Azure Storage. Ha nem szeretné, hogy a csomag nyilvános legyen, az URL-címben egy [sas](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) -tokent is hozzáadhat. A virtuális gépekhez [szolgáltatási végpontot](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) is alkalmazhat a magánhálózaton, bár ez a konfiguráció csak a csomag elérésére és a szolgáltatással való kommunikációra vonatkozik.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>A titkok használata a vendég konfigurációs csomagjaiban

Azure Policy vendég konfigurációban a futtatáskor használt titkok kezelésének optimális módja, ha a Azure Key Vault tárolja őket. Ez a kialakítás az egyéni DSC-erőforrásokon belül valósítható meg.

Először hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az Azure-ban. Az identitást a virtuális gépek használják a Key Vaultban tárolt titkos kódok elérésére. A részletes lépésekért lásd: [felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása és törlése Azure PowerShell használatával](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

Ezután hozzon létre egy Key Vault példányt. Részletes lépések: [titkos PowerShell beállítása és](../../../key-vault/quick-create-powershell.md)beolvasása.
Rendeljen engedélyeket a példányhoz, hogy a felhasználó által hozzárendelt identitás hozzáférést biztosítson a Key Vaultban tárolt titkos kulcsokhoz. Részletes lépések: [Secret-.net beállítása és](../../../key-vault/quick-create-net.md#assign-permissions-to-your-application-to-read-secrets-from-key-vault)beolvasása.

Ezután rendelje hozzá a felhasználó által hozzárendelt identitást a virtuális géphez. A részletes lépésekért lásd: felügyelt identitások konfigurálása Azure-beli virtuális gépeken [a PowerShell használatával](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
A skálán ezt az identitást a Azure Resource Manager segítségével Azure Policy használatával rendelheti hozzá. A részletes lépésekért lásd: felügyelt identitások konfigurálása Azure-beli virtuális gépeken [sablon használatával](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

Végül az egyéni erőforráson belül a fentiekben generált ügyfél-azonosítót használva férhet hozzá Key Vault a számítógépről elérhető token használatával. A Key Vault példányának [](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema) ésURL-címénekértékekéntadhatókátazerőforrásnak,ígyazerőforrástnemkelltöbbkörnyezetesetébenfrissíteni,vagyhaazértékeketmódosítani`client_id` kell.

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

A konfigurációs csomag létrehozása után, de az Azure-ba való közzététel előtt tesztelheti a csomag funkcióit a munkaállomás vagy a CI/CD-környezet használatával. A GuestConfiguration modul egy olyan parancsmagot `Test-GuestConfigurationPackage` tartalmaz, amely az Azure-beli virtuális gépeken belül használt fejlesztési környezetben ugyanazt az ügynököt tölti be. Ezzel a megoldással helyileg is elvégezheti az integrációs tesztelést, mielőtt kiadná a kiszámlázott tesztelési/QA/éles környezeteket.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

A `Test-GuestConfigurationPackage` parancsmag paraméterei:

- **Név**: A vendég konfigurációs szabályzatának neve.
- **Paraméter**: Szórótábla formátumban megadott házirend-paraméterek.
- **Elérési út**: A vendég konfigurációs csomag teljes elérési útja.

A parancsmag a PowerShell-folyamatból is támogatja a bemenetet. A `New-GuestConfigurationPackage` parancsmag kimenetének átadása `Test-GuestConfigurationPackage` a parancsmagnak.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

A paraméterekkel való teszteléssel kapcsolatos további információkért lásd az alábbi szakaszt az [Egyéni vendég konfigurációs szabályzatokban található paraméterek használatával](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>A Azure Policy-definíció és-kezdeményezés telepítési fájljainak létrehozása

Miután létrehozott egy egyéni házirend-csomagot, és a virtuális gépek által elérhető helyre lett feltöltve, hozza létre a vendég-konfigurációs házirend definícióját a Azure Policyhoz. A `New-GuestConfigurationPolicy` parancsmag nyilvánosan elérhető vendég-konfiguráció egyéni házirend-csomagot hoz létre, és létrehoz egy **auditIfNotExists** és egy **deployIfNotExists** szabályzat-definíciót. Egy házirend-kezdeményezési definíció is létrejön, amely magában foglalja mindkét házirend-definíciót is.

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

- **ContentUri**: A vendég-konfiguráció tartalmi csomagjának nyilvános http (s) URI azonosítója.
- **DisplayName**: A házirend megjelenítendő neve.
- **Leírás**: Házirend leírása.
- **Paraméter**: Szórótábla formátumban megadott házirend-paraméterek.
- **Verzió**: Házirend verziója.
- **Elérési út**: A cél elérési útja, ahol a szabályzat-definíciók létrejönnek.
- **Platform**: Cél platform (Windows/Linux) a vendég konfigurációs házirendjéhez és a tartalmi csomaghoz.

A következő fájlokat hozza létre `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Initiative. JSON**

A parancsmag kimenete egy olyan objektumot ad vissza, amely a házirend-fájlok kezdeményezésének megjelenítendő nevét és elérési útját tartalmazza.

Ha ezt a parancsot egy egyéni házirend-projekt beépítéséhez szeretné használni, módosíthatja ezeket a fájlokat. Például a "If" szakasz módosításával kiértékelheti, hogy van-e egy adott címke a virtuális gépekhez. A házirendek létrehozásával kapcsolatos részletes információkat a [szabályzatok programozott létrehozása](./programmatically-create.md)című témakörben talál.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Paraméterek használata az egyéni vendég-konfigurációs házirendekben

A vendég konfiguráció futási időben támogatja a konfiguráció felülírási tulajdonságait. Ez a funkció azt jelenti, hogy a csomagban lévő MOF-fájlban lévő értékeket nem kell statikusnak tekinteni. A felülbírálási értékek a Azure Policyon keresztül érhetők el, és nem befolyásolják a konfigurációk létrehozási vagy fordítási módját.

A parancsmagok `New-GuestConfigurationPolicy` `Test-GuestConfigurationPolicyPackage` tartalmazzák a **Paraméterek**nevű paramétert.
Ez a paraméter egy szórótábla-definíciót vesz fel, amely tartalmazza az egyes paraméterek összes részletét, és automatikusan létrehozza az egyes Azure Policy-definíciók létrehozásához használt fájlok összes szükséges részét.

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

Linux-házirendek esetében foglalja bele a `AttributesYmlContent` tulajdonságot a konfigurációba, és ennek megfelelően írja felül az értékeket. A vendég konfigurációs ügynök automatikusan létrehozza a YaML-fájlt, amelyet az inspec az attribútumok tárolására használ. Lásd az alábbi példát.

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

A **GuestConfiguration** -erőforrás modul lehetővé teszik, hogy mindkét házirend-definíciót és az Azure-beli kezdeményezési definíciót `Publish-GuestConfigurationPolicy` a parancsmagon keresztül egy lépéssel hozza létre.
A parancsmag csak a **path** paraméterrel rendelkezik, amely a által `New-GuestConfigurationPolicy`létrehozott három JSON-fájl helyére mutat.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

A `Publish-GuestConfigurationPolicy` parancsmag fogadja az elérési utat a PowerShell-folyamatból. Ez a funkció azt jelenti, hogy létrehozhatja a házirend-fájlokat, és közzéteheti őket egyetlen vezetékes parancsban.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Az Azure-ban létrehozott házirend-és kezdeményezési definíciókkal az utolsó lépés a kezdeményezés kiosztása. Lásd: a kezdeményezés társítása a [portál](../assign-policy-portal.md), az [Azure CLI](../assign-policy-azurecli.md)és a [Azure PowerShell](../assign-policy-powershell.md)használatával.

> [!IMPORTANT]
> A _AuditIfNotExists_ -és _DeployIfNotExists_ -házirendeket egyesítő kezdeményezéssel **mindig** hozzá kell rendelni a vendég-konfigurációs házirendeket. Ha csak a _AuditIfNotExists_ szabályzat van hozzárendelve, az előfeltételek nincsenek telepítve, és a házirend mindig azt mutatja, hogy a "0" kiszolgálók megfelelőek.

## <a name="policy-lifecycle"></a>Szabályzat életciklusa

Miután közzétett egy egyéni Azure Policy az egyéni tartalomkezelő csomag használatával, két mezőt kell frissíteni, ha közzé szeretne tenni egy új kiadást.

- **Verzió**: A parancsmag `New-GuestConfigurationPolicy` -parancsmag futtatásakor meg kell adnia a jelenleg közzétett verziónál nagyobb verziószámot.  Ezzel frissíti a vendég konfiguráció-hozzárendelés verzióját az új házirend-fájlban, így a bővítmény felismeri, hogy a csomag frissítve lett.
- **contentHash**: Ezt a `New-GuestConfigurationPolicy` parancsmag automatikusan frissíti.  A által `New-GuestConfigurationPackage`létrehozott csomag kivonatának értéke.  Ennek megfelelőnek kell lennie `.zip` a közzétett fájlhoz.  Ha csak a `contentUri` tulajdonság frissül, például abban az esetben, ha valaki manuálisan módosíthatja a házirend-definíciót a portálon, a bővítmény nem fogadja el a csomagot.

Egy frissített csomag kiadásának legegyszerűbb módja, ha megismétli a jelen cikkben ismertetett folyamatot, és megadja a verziószámot.
Ez garantálja az összes tulajdonság megfelelő frissítését.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows Csoportházirend tartalom konvertálása Azure Policy vendég konfigurációra

A vendég konfigurációja a Windows rendszerű gépek naplózásakor a PowerShell desired State Configuration szintaxisának implementációja.
A DSC-Közösség közzétette az exportált Csoportházirend-sablonok DSC formátumra való konvertálásának eszközét.
Az eszköznek a fent ismertetett vendég konfigurációs parancsmagokkal együtt történő használatával átalakíthatja a Windows Csoportházirend tartalmát, és becsomagolhatja vagy közzéteheti a Azure Policy a naplózáshoz.
Az eszköz használatával kapcsolatos részletekért lásd a gyors üzembe [helyezést ismertető cikket. Csoportházirend konvertálása a DSC](/powershell/dsc/quickstarts/gpo-quickstart)-be.
A tartalom konvertálása után a fenti lépéseket követve hozzon létre egy pakcage, és tegye közzé Azure Policyként ugyanúgy, mint bármely DSC-tartalomhoz.

## <a name="optional-signing-guest-configuration-packages"></a>VÁLASZTHATÓ Vendég konfigurációs csomagjainak aláírása

A vendég konfigurációja egyéni szabályzatok alapértelmezés szerint a SHA256-kivonattal ellenőrzik, hogy a házirend-csomag nem módosult-e a közzétételkor, amikor a naplózott kiszolgáló beolvassa.
Igény szerint az ügyfelek is használhatnak tanúsítványokat a csomagok aláírására és a vendég konfigurációs bővítmény kényszerítésére, hogy csak az aláírt tartalmat engedélyezzék.

Ennek a forgatókönyvnek az engedélyezéséhez két lépést kell végrehajtania. Futtassa a parancsmagot a tartalmi csomag aláírásához, és fűzze hozzá a címkét a virtuális gépekhez, amelyeknek a kód aláírását kell megkövetelni.

Az aláírás-ellenőrzési funkció használatához futtassa a `Protect-GuestConfigurationPackage` parancsmagot a csomag aláírásához a közzététel előtt. Ehhez a parancsmaghoz "kód aláírása" tanúsítvány szükséges.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

A `Protect-GuestConfigurationPackage` parancsmag paraméterei:

- **Elérési út**: A vendég konfigurációs csomag teljes elérési útja.
- **Tanúsítvány**: Kód aláíró tanúsítványa a csomag aláírásához. Ez a paraméter csak Windows-tartalmak aláírása esetén támogatott.
- **PrivateGpgKeyPath**: Saját GPG-kulcs elérési útja. Ez a paraméter csak a Linux-tartalmak aláírása esetén támogatott.
- **PublicGpgKeyPath**: Nyilvános GPG kulcs elérési útja. Ez a paraméter csak a Linux-tartalmak aláírása esetén támogatott.

A GuestConfiguration-ügynök elvárja, hogy a tanúsítvány nyilvános kulcsa a Windows rendszerű gépeken és a Linux-gépek elérési útjában `/usr/local/share/ca-certificates/extra` legyen jelen a "megbízható legfelső szintű hitelesítésszolgáltatók" szolgáltatásban. Ahhoz, hogy a csomópont ellenőrizze az aláírt tartalmat, telepítse a tanúsítvány nyilvános kulcsát a virtuális gépre az egyéni házirend alkalmazása előtt. Ez a folyamat a virtuális gépen belüli bármely technikával vagy Azure Policy használatával végezhető el. [Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)található egy példa erre a sablonra.
Az Key Vault hozzáférési szabályzatnak lehetővé kell tennie a számítási erőforrás-szolgáltató számára a tanúsítványok elérését a központi telepítések során. A részletes lépésekért lásd: [Key Vault beállítása virtuális gépekhez a Azure Resource Managerban](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Az alábbi példa arra szolgál, hogy egy aláíró tanúsítványból exportálja a nyilvános kulcsot a virtuális gépre való importáláshoz.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

A Linux rendszerű virtuális gépekhez használható GPG-kulcsok létrehozásának jó referenciája a GitHubon található cikk, [új GPG-kulcs](https://help.github.com/en/articles/generating-a-new-gpg-key)létrehozása.

A tartalom közzététele után fűzze hozzá a nevet `GuestConfigPolicyCertificateValidation` és az értéket `enabled` tartalmazó címkét az összes olyan virtuális géphez, amelynél szükség van a kód aláírására. Ezt a címkét a Azure Policy használatával lehet méretezni. Lásd: [címke alkalmazása és az alapértelmezett érték](../samples/apply-tag-default-value.md) minta.
A címke betartása után a `New-GuestConfigurationPolicy` parancsmag használatával generált szabályzat-definíció engedélyezi a követelményt a vendég konfigurációs bővítményen keresztül.

## <a name="next-steps"></a>További lépések

- Tudnivalók a virtuális gépek a [vendég konfigurációjával](../concepts/guest-configuration.md)való naplózásáról.
- Megtudhatja, hogyan [hozhat létre programozott](programmatically-create.md)módon házirendeket.
- Ismerje meg, hogyan kérheti le a [megfelelőségi információkat](getting-compliance-data.md).
