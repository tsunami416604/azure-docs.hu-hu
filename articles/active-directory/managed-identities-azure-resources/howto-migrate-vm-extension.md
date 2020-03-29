---
title: A felügyelt identitásvirtuálisgép-bővítmény használatának leállítása – Azure AD
description: Lépésenkénti utasítások a virtuális gépbővítmény használatának leállításához és az Azure Instance metaadat-szolgáltatás (IMDS) hitelesítéshez való használatának megkezdéséhez.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049064"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>A virtuálisgép által felügyelt identitásbővítmény használatának leállítása és az Azure-példány metaadatszolgáltatásának használatának megkezdése

## <a name="virtual-machine-extension-for-managed-identities"></a>Virtuálisgép-bővítmény felügyelt identitásokhoz

A felügyelt identitások virtuálisgép-bővítmény segítségével jogkivonatokat kérhet a virtuális gépen belül felügyelt identitáshoz. A munkafolyamat a következő lépésekből áll:

1. Először az erőforráson belüli számítási `http://localhost/oauth2/token` feladatok meghívja a helyi végpontot egy hozzáférési jogkivonat kérésére.
2. A virtuálisgép-bővítmény ezután a felügyelt identitás hitelesítő adatait használja egy hozzáférési jogkivonat kéréséhez az Azure AD-től.The virtual machine extension then uses the credentials for the managed identity, to request a access tokent from Azure AD.. 
3. A hozzáférési jogkivonat visszakerül a hívó, és az Azure AD-hitelesítést támogató szolgáltatások, például az Azure Key Vault vagy az Azure Storage hitelesítésére használható.

A következő szakaszban ismertetett számos korlátozás miatt a felügyelt identitásvirtuális gép bővítménye elavult az Azure Instance metaadatszolgáltatás (IMDS) megfelelő végpontjának használata érdekében.

### <a name="provision-the-extension"></a>A kiterjesztés biztosítása 

Ha egy virtuális gép vagy virtuálisgép-méretezési készlet felügyelt identitással konfigurál, tetszés szerint dönthet úgy, `-Type` hogy a [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) parancsmag paraméterével kiépíti az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásait. A virtuális `ManagedIdentityExtensionForWindows` gép `ManagedIdentityExtensionForLinux`típusától függően vagy a , és `-Name` a paraméter használatával elnevezheti azt. A `-Settings` paraméter megadja az OAuth token végpontja által a tokenek megszerzéséhez használt portot:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Az Azure Resource Manager központi telepítési sablon használatával is kiépítheti a virtuálisgép-bővítményt, ha hozzáadja a következő JSON-t a szakaszhoz a `resources` sablonhoz (a Linux-verzió nevéhez és típuselemeihez használható). `ManagedIdentityExtensionForLinux`

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
    
    
Ha virtuálisgép-méretezési készletekkel dolgozik, az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag használatával kiépítheti az Azure-erőforrások virtuálisgép-méretezési csoportbővítményének felügyelt identitásait is. A virtuálisgép-méretezési csoport típusától függően vagy `ManagedIdentityExtensionForWindows` `ManagedIdentityExtensionForLinux`a , `-Name` és elnevezheti azt a paraméter használatával. A `-Settings` paraméter megadja az OAuth token végpontja által a tokenek megszerzéséhez használt portot:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
A virtuálisgép-méretezési csoport bővítményének kiépítése az Azure Resource Manager központi `extensionpProfile` telepítési sablonnal, `ManagedIdentityExtensionForLinux` adja hozzá a következő JSON-t a szakaszhoz a sablonhoz (a Linux-verzió nevéhez és típuselemeihez használható).

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

A virtuálisgép-bővítmény kiépítése DNS-keresési hibák miatt sikertelen lehet. Ebben az esetben indítsa újra a virtuális gépet, majd próbálkozzon újra. 

### <a name="remove-the-extension"></a>A bővítmény eltávolítása 
A bővítmény eltávolításához `-n ManagedIdentityExtensionForWindows` `-n ManagedIdentityExtensionForLinux` használja vagy váltsa át (a virtuális gép típusától függően) az [az vm-bővítmény törlése](https://docs.microsoft.com/cli/azure/vm/), vagy az az [vmss bővítmény törlése](https://docs.microsoft.com/cli/azure/vmss) az Azure CLI-t használó virtuálisgép-méretezési készletekhez vagy `Remove-AzVMExtension` a Powershellhez:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Token beszerzése a virtuálisgép-bővítmény használatával

Az alábbiakban egy mintakérelem az Azure-erőforrások virtuálisgép-bővítményvégpontfelügyelt identitásait használó:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elem | Leírás |
| ------- | ----------- |
| `GET` | A HTTP-művelet, amely azt jelzi, hogy adatokat szeretne beolvasni a végpontból. Ebben az esetben egy OAuth hozzáférési jogkivonat. | 
| `http://localhost:50342/oauth2/token` | Az Azure-erőforrások végpontjának felügyelt identitásai, ahol az 50342 az alapértelmezett port, és konfigurálható. |
| `resource` | A lekérdezési karakterlánc paraméter, amely a célerőforrás alkalmazásazonosítóuri-ját jelzi. A kiadott jogkivonat `aud` (közönség) jogcímében is megjelenik. Ez a példa egy jogkivonatot kér az Azure Resource `https://management.azure.com/`Manager eléréséhez, amely alkalmazásazonosító URI-val rendelkezik. |
| `Metadata` | Egy HTTP-kérelem fejlécmezője, amelyet az Azure-erőforrások felügyelt identitásai igényelnek a kiszolgálóoldali kérelemhamisítás (SSRF) támadáselleni megoldásként. Ezt az értéket "igaz" értékre kell állítani, minden kisbetűvel.|
| `object_id` | (Nem kötelező) Egy lekérdezési karakterlánc paraméter, amely a felügyelt identitás object_id jelzi, amelyhez a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitások rendelkezik.|
| `client_id` | (Nem kötelező) Egy lekérdezési karakterlánc paraméter, amely a felügyelt identitás client_id jelzi, amelyhez a jogkivonatot szeretné. Kötelező, ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitások rendelkezik.|


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
| `access_token` | A kért hozzáférési jogkivonat. Biztonságos REST API hívása kor a `Authorization` jogkivonat "tulajdonosi" jogkivonatként van beágyazva a kérelem fejlécmezőjébe, lehetővé téve az API számára a hívó hitelesítését. | 
| `refresh_token` | Az Azure-erőforrások felügyelt identitásai nem használják. |
| `expires_in` | A hozzáférési jogkivonat a kiállítás időpontjától számított másodpercek száma, mielőtt lejárna. A kiállítás ideje megtalálható a jogkivonat jogcímében. `iat` |
| `expires_on` | A hozzáférési jogkivonat lejáratának időtartománya. A dátum az "1970-01-01T0:0:0Z UTC" (a jogkivonat jogcímének `exp` megfelelő) másodpercek számaként jelenik meg. |
| `not_before` | A hozzáférési jogkivonat érvénybe lépésének és elfogadott időtartamának. A dátum az "1970-01-01T0:0:0Z UTC" (a jogkivonat jogcímének `nbf` megfelelő) másodpercek számaként jelenik meg. |
| `resource` | Az erőforrás, amelyhez hozzáférési jogkivonatot kértek, amely megfelel a `resource` kérelem lekérdezési karakterlánc paraméterének. |
| `token_type` | A jogkivonat típusa, amely egy "tulajdonos" hozzáférési jogkivonat, ami azt jelenti, hogy az erőforrás hozzáférést biztosíthat a jogkivonat tulajdonosához. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>A virtuálisgép-bővítmény hibáinak elhárítása 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>A virtuálisgép-bővítmény újraindítása hiba után

Windows és a Linux bizonyos verzióiban, ha a kiterjesztés leáll, a következő parancsmag használható manuálisan újraindítani:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Az elemek magyarázata: 
- A Windows bővítményneve és típusa a következő:`ManagedIdentityExtensionForWindows`
- A Linux kiterjesztés neve és típusa:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Az "Automation script" sikertelen, amikor az Azure-erőforrások bővítmény felügyelt identitások rendszerének exportálását kíséreli meg

Ha az Azure-erőforrások felügyelt identitásai engedélyezve vannak egy virtuális gépen, a következő hiba jelenik meg, amikor megpróbálja használni az "Automation script" funkciót a virtuális gép, vagy annak erőforrás-csoport:

![Felügyelt identitások az Azure-erőforrások automatizálási parancsfájljának exportálási hibájához](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Az Azure-erőforrások virtuálisgép-bővítmény felügyelt identitásai jelenleg nem támogatják a séma erőforráscsoport-sablonba történő exportálásának lehetőségét. Ennek eredményeképpen a létrehozott sablon nem jeleníti meg a konfigurációs paramétereket az Azure-erőforrások felügyelt identitások engedélyezéséhez az erőforráson. Ezek a szakaszok manuálisan is hozzáadhatók, ha követik az [Azure-erőforrások felügyelt identitásának konfigurálása egy Azure-alapú virtuális gépen egy sablon használatával című példákat.](qs-configure-template-windows-vm.md)

Amikor a sémaexportálási funkció elérhetővé válik az Azure-erőforrások virtuálisgép-bővítményének felügyelt identitásai számára (2019 januárjában tervezik az evevést), akkor az [a virtuálisgép-bővítményeket tartalmazó Erőforráscsoportok exportálása](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)listában jelenik meg.

## <a name="limitations-of-the-virtual-machine-extension"></a>A virtuálisgép-bővítmény korlátai 

A virtuálisgép-bővítmény használatának számos fő korlátozása van. 

 * A legsúlyosabb korlátozás az a tény, hogy a jogkivonatok kéréséhez használt hitelesítő adatok a virtuális gépen tárolódnak. A támadó, aki sikeresen megsérti a virtuális gépet, kitudja irtani a hitelesítő adatokat. 
 * Továbbá a virtuális gép kiterjesztését még mindig nem támogatja több Linux disztribúció, és hatalmas fejlesztési költség gel módosíthatja, felépítheti és tesztelheti a kiterjesztést az egyes disztribúciókon. Jelenleg csak a következő Linux disztribúciók támogatottak: 
    * CoreOS stabil
    * CentOS 7,1 
    * Piros kalap 7,2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * A felügyelt identitásokkal rendelkező virtuális gépek üzembe helyezése hatással van a teljesítményre, mivel a virtuálisgép-bővítményt is ki kell építeni. 
 * Végül a virtuálisgép-bővítmény csak támogatja, hogy virtuális gépenként 32 felhasználó által hozzárendelt felügyelt identitáslegyen. 

## <a name="azure-instance-metadata-service"></a>Azure-példány metaadat-szolgáltatása

Az [Azure Instance metaadat-szolgáltatás (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) egy REST-végpont, amely tájékoztatást nyújt a virtuális gép példányok futtatásához, amelyek a virtuális gépek kezelésére és konfigurálására használható. A végpont egy jól ismert, nem irányítható IP-címen (`169.254.169.254`érhető el, amely csak a virtuális gépen belül érhető el.

Számos előnye van az Azure IMDS jogkivonatok kéréséhez. 

1. A szolgáltatás a virtuális gépen kívül található, ezért a felügyelt identitások által használt hitelesítő adatok már nem találhatók meg a virtuális gépen. Ehelyett azok üzemeltetése és biztonságos az Azure virtuális gép gazdagépén.   
2. Az Azure IaaS által támogatott összes Windows- és Linux-operációs rendszer használhatja a felügyelt identitásokat.
3. A telepítés gyorsabb és egyszerűbb, mivel a virtuálisgép-bővítmény már nem kell kiépíteni.
4. Az IMDS-végpontdal legfeljebb 1000 felhasználó által hozzárendelt felügyelt identitás rendelhető egyetlen virtuális géphez.
5. Nincs jelentős változás a kérelmek et használó IMDS, szemben a virtuális gép bővítmény, ezért meglehetősen egyszerű port a meglévő központi telepítések, amelyek jelenleg a virtuális gép bővítményt.

Ezen okok miatt az Azure IMDS-szolgáltatás lesz a defacto módja jogkivonatok kérésének, miután a virtuálisgép-bővítmény elavult. 


## <a name="next-steps"></a>Következő lépések

* [Felügyelt identitások használata Azure-erőforrásokhoz egy Azure virtuális gépen hozzáférési jogkivonat beszerzéséhez](how-to-use-vm-token.md)
* [Azure-példány metaadat-szolgáltatása](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
