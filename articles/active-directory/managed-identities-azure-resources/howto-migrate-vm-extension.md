---
title: Állítsa le a felügyelt identitás Virtuálisgép-bővítménnyel, és indítsa el az Azure Instance Metadata szolgáltatás végpont használatával
description: Lépés útmutató leállítása, a Virtuálisgép-bővítménnyel, és indítsa el az Azure példány metaadat szolgáltatás (IMDS) használnak a hitelesítéshez.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: priyamo
ms.openlocfilehash: c29eb22f2606fc44cfd7d948f2c363cb51c811dd
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227616"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Állítsa le a virtuális gép használatával felügyelt identitások bővítményt, és elkezdheti az Azure Instance Metadata szolgáltatás

## <a name="virtual-machine-extension-for-managed-identities"></a>Felügyelt identitások virtuálisgép-bővítmény

Felügyelt identitások virtuálisgép-bővítmény segítségével kérelem tokenek egy felügyelt identitás, a virtuális gépen. A munkafolyamat a következő lépésekből áll:

1. Először az erőforrás tevékenységprofiljának meghívja a helyi végpont `http://localhost/oauth2/token` hozzáférési jogkivonat kérése.
2. Virtuálisgép-bővítmény majd használja a felügyelt identitás hitelesítő adatait az Azure AD hozzáférési jogkivonatot kérhet.. 
3. A hozzáférési jogkivonatot a rendszer visszaadja a hívó, és használható, amelyek támogatják az Azure AD-hitelesítés, mint például az Azure Key Vaultot vagy az Azure Storage-szolgáltatásokhoz való hitelesítéséhez.

A következő szakaszban leírt több korlátozások miatt a felügyelt identitás Virtuálisgép-bővítmény használata helyett az egyenértékű végpont használatával az Azure példány metaadat szolgáltatás (IMDS)

### <a name="provision-the-extension"></a>A bővítmény kiépítése 

Amikor konfigurál egy virtuális gép vagy virtuálisgép-méretezési csoport egy felügyelt identitás van beállítva, akkor előfordulhat, hogy nem kötelező váltani, igény szerint dönthet úgy, hogy az Azure-erőforrások virtuális gép bővítmény használatával a felügyelt identitásokból kiépítése a `-Type` a paraméterrel[ Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) parancsmagot. Átadhat `ManagedIdentityExtensionForWindows` vagy `ManagedIdentityExtensionForLinux`, attól függően, a virtuális gép típusát, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Is használhatja az Azure Resource Manager központi telepítési sablont a Virtuálisgép-bővítmény kiépítése a következő JSON-ra való hozzáadásával a `resources` a sablon szakasszal (használata `ManagedIdentityExtensionForLinux` a nevű és típusú elemek, a Linux-verzió).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Ha dolgozik, a virtual machine scale sets, is telepíthet a felügyelt identitásokból Azure-erőforrások virtuális gép méretezési bővítmény használatával a [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmagot. Átadhat `ManagedIdentityExtensionForWindows` vagy `ManagedIdentityExtensionForLinux`, virtuálisgép-méretezési csoport típusától függően állítsa be, és adja neki a használatával a `-Name` paraméter. A `-Settings` paraméter adja meg a token beszerzéséhez az OAuth jogkivonat-végpont által használt port:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
A virtuálisgép-méretezési csoport létrehozásához állítsa be a bővítmény az Azure Resource Manager üzembe helyezési sablon, adja hozzá a következő JSON-a `extensionpProfile` a sablon szakasszal (használata `ManagedIdentityExtensionForLinux` a nevű és típusú elemek, a Linux-verzió).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

A virtuális gépi bővítmény kiépítése a DNS-keresési hibák miatt meghiúsulhat. Ha ez történik, indítsa újra a virtuális gépet, és próbálkozzon újra. 

### <a name="remove-the-extension"></a>Távolítsa el a bővítményt 
A bővítmény eltávolításához használja `-n ManagedIdentityExtensionForWindows` vagy `-n ManagedIdentityExtensionForLinux` kapcsoló (virtuális gép függően) és [vm-bővítmény törlése az](https://docs.microsoft.com/cli/azure/vm/), vagy [az vmss-bővítmény törlése](https://docs.microsoft.com/cli/azure/vmss) a virtuálisgép-méretezési csoport Beállítja az Azure CLI használatával vagy `Remove-AzVMExtension` PowerShell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>A virtuális gépi bővítmény használatával jogkivonat beszerzése

A következő egy minta kérelmet a felügyelt identitások használatával az Azure-erőforrások Virtuálisgép-bővítmény végpont:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-műveletet, amely azt jelzi, hogy szeretne-adatokat lekérni a végpontot. Ebben az esetben az OAuth hozzáférési tokent. | 
| `http://localhost:50342/oauth2/token` | A felügyelt identitások Azure-erőforrások végponton, ahol az alapértelmezett port 50342, és konfigurálható. |
| `resource` | A lekérdezési sztring paramétereként, az Alkalmazásazonosító URI a célként megadott erőforrás-jelző. Emellett megjelenik a `aud` (célközönség) jogcím a kiállított jogkivonat. Ebben a példában az Azure Resource Manager eléréséhez tokent kér az Alkalmazásazonosító URI-t, amelynek https://management.azure.com/. |
| `Metadata` | Egy HTTP kérelem fejléce, kötelező mező által felügyelt identitásokat az Azure-erőforrások, a kiszolgáló kiszolgálóoldali kérelmet hamisítására (SSRF) támadások elleni megoldás. Ezt az értéket állítsa "true", csupa kisbetű szerepel.|
| `object_id` | (Nem kötelező) A lekérdezési sztring paramétereként, a object_id az felügyelt identitás szeretné token jelzi. Szükséges, ha a virtuális gépen több felhasználó által hozzárendelt felügyelt identitást.|
| `client_id` | (Nem kötelező) A lekérdezési sztring paramétereként, a client_id az felügyelt identitás szeretné token jelzi. Szükséges, ha a virtuális gépen több felhasználó által hozzárendelt felügyelt identitást.|


Mintaválasz:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elem | Leírás |
| ------- | ----------- |
| `access_token` | A kért hozzáférési jogkivonatot. Egy biztonságos REST API hívásakor a token be van ágyazva a `Authorization` kérelem fejléce mező "tulajdonosi" jogkivonattal, így az API-t a hitelesítés a hívó. | 
| `refresh_token` | Nem használja a felügyelt identitások az Azure-erőforrásokhoz. |
| `expires_in` | A hozzáférési jogkivonat továbbra is érvényes, mielőtt lejár, a kiállítási idején másodpercek számát. Kiadás időpontja a jogkivonatban található `iat` jogcím. |
| `expires_on` | Az időtartomány, ha a hozzáférési jogkivonat lejár. A dátum jelenik meg a másodpercek számát "1970-01-01T0:0:0Z (UTC)" (felel meg a token `exp` jogcím). |
| `not_before` | Az időtartomány, ha a hozzáférési jogkivonat érvénybe lép, és elfogadható. A dátum jelenik meg a másodpercek számát "1970-01-01T0:0:0Z (UTC)" (felel meg a token `nbf` jogcím). |
| `resource` | Az erőforrás a hozzáférési jogkivonatot a kért, mely megfelel a `resource` lekérdezési karakterlánc paraméter a kérelem. |
| `token_type` | A jogkivonatot, amely a "Tulajdonos" hozzáférési jogkivonatot, ami azt jelenti, hogy az erőforrás segítségével hozzáférést biztosíthat a token a tulajdonosi típusa. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Virtuálisgép-bővítmény hibáinak elhárítása 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Indítsa újra a virtuálisgép-bővítmény egy meghibásodás után

A Windows és Linux-bizonyos verziók Ha leáll a bővítményt, a következő parancsmag használható indítsa újra manuálisan:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- Bővítmény neve és típusa, a Windows a következő: `ManagedIdentityExtensionForWindows`
- Bővítmény neve és a Linux rendszeren írja be a következő: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automation-szkript" sikertelen lesz, amikor megpróbálja felügyelt identitások Azure-erőforrás-kiterjesztés export schématu.

Ha felügyelt identitások az Azure-erőforrások virtuális gépen engedélyezve van, használja az "Automation-szkript" funkciót a virtuális gép vagy a saját erőforráscsoportján megkísérlésekor a következő hiba jelenik meg:

![Az Azure-erőforrások automation-szkript felügyelt identitások exportálási hiba](./media/howto-migrate-vm-extension/automation-script-export-error.png)

A felügyelt identitások az Azure-erőforrások virtuális gépi bővítmény jelenleg nem támogatja a séma egy erőforráscsoport sablonjának exportálása lehetővé teszi. Ennek eredményeképpen a létrejött sablon nem jeleníti meg konfigurációs paraméter egy felügyelt identitások az Azure-erőforrásokhoz az erőforráson. Ezek a szakaszok manuálisan is hozzáadhatók a következő szereplő példák [konfigurálása felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen, a sablonok használatával](qs-configure-template-windows-vm.md).

Ha a séma exportálási funkciót az Azure-erőforrások virtuális gépi bővítmény (tervezett elavult a január 2019 esetében) a felügyelt identitások számára elérhetővé válik, az lesz jelenik meg [exportálása erőforrás tartalmazó csoportok Virtuálisgép-bővítmények ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>A virtuális gépi bővítmény korlátozásai 

Van néhány jelentős korlátozás, a virtuális gépi bővítmény használatával. 

 * A legsúlyosabb korlátozása, az a tény, hogy a jogkivonatok kéréséhez használt hitelesítő adatok tárolása a virtuális gépen. A támadó sikeresen feltöri a virtuális gép is próbál kiszűrni a rendszerből a hitelesítő adatokat. 
 * Továbbá a virtuális gépi bővítmény továbbra is nem támogatja a számos Linux-disztribúció, egy óriási fejlesztési költség módosítása, létrehozása és tesztelése a bővítményt, az egyes azokat a disztribúciókat. Jelenleg csak a következő Linux-disztribúciók támogatottak: 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Nincs teljesítménycsökkenést felügyelt identitások, a virtuális gépek üzembe helyezéséről, mert a virtuálisgép-bővítmény is ki kell építeni. 
 * Végül a virtuális gépi bővítmény csak támogatja 32 felhasználó által hozzárendelt felügyelt identitások virtuális gépenként kellene. 

## <a name="azure-instance-metadata-service"></a>Az Azure Instance Metadata szolgáltatás

A [Azure példány metaadat szolgáltatás (IMDS)](https://docs.microsoft.com/azure/virtual-machines/instance-metadata-service) egy REST-végpont, amely azzal foglalkozik, amelyek segítségével kezelheti és konfigurálhatja a virtuális gépek virtuálisgép-példányokat futtató. A végpont egy jól ismert nem átirányítható IP-címen érhető el (`169.254.169.254`), amelyek elérhetők csak a virtuális gépen.

Nincsenek Azure IMDS használata szükséges jogkivonatok kérelmezésére számos előnnyel jár. 

1. A szolgáltatás a virtuális gép külső, ezért a felügyelt identitásokból által használt hitelesítő adatok már nem találhatók a virtuális gépen. Ehelyett ezeket üzemeltetett és a gazdagépen az Azure virtuális gép védett.   
2. Az összes Windows és Linux operációs rendszer támogatott az Azure IaaS felügyelt identitások használhatja.
3. Üzembe helyezés gyorsabb és egyszerűbb, azért, mert a Virtuálisgép-bővítmény már nem kell építeni.
4. A IMDS a végpont, legfeljebb 1000 felhasználó által hozzárendelt felügyelt identitások egyetlen virtuális gép is hozzárendelhető.
5. Nem jelentős változik a kérelmekre, IMDS használatával ellentétben ezek a virtuális gépi bővítmény használatával, ezért a viszonylag egyszerű porton keresztül a meglévő üzemelő példányok, amelyek jelenleg használják a virtuálisgép-bővítmény.

Ebből kifolyólag a Azure IMDS szolgáltatás után a virtuális gépi bővítmény elavult lesz szükséges jogkivonatok kérelmezésére tényleges módja. 


## <a name="next-steps"></a>További lépések

* [Felügyelt identitások használata az Azure-erőforrások egy Azure virtuális gépen a hozzáférési jogkivonat beszerzése](how-to-use-vm-token.md)
* [Az Azure Instance Metadata szolgáltatás](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
