---
title: Az Azure-beli SQL Server virtuális gép licencelési modellt módosítása
description: Ismerkedjen meg hogyan lehet váltani, az SQL virtuális gép az Azure-ban a "használatalapú fizetés" licencelési "bring-your-saját licenc" az Azure Hybrid Benefit használatával.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786769"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Az Azure-beli SQL Server virtuális gép licencelési modellt módosítása
Ez a cikk bemutatja, hogyan módosíthatja az Azure-ban az új SQL Server virtuális gép licencelési modelljét SQL virtuális gép erőforrás-szolgáltató – **Microsoft.SqlVirtualMachine**.

Nincsenek két engedélyezési modellel üzemeltető SQL Server - egy virtuális gépen (VM) használatalapú fizetés és az Azure Hybrid Benefit (AHB). És most az Azure portal, az Azure parancssori felület vagy PowerShell használatával módosíthatja az SQL Server virtuális gép licencelési modelljét. 

A **utólagos elszámolású** (PAYG) modell azt jelenti, hogy az a másodpercenkénti költség, az Azure virtuális Gépen futó tartalmazza-e az SQL Server-licenc költsége.
A [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) lehetővé teszi, hogy az SQL Server rendszerű virtuális gép saját SQL Server-licencét. 

A Microsoft Azure Hybrid Benefittel az SQL Server lehetővé teszi, hogy az SQL Server-licencek használata a frissítési garancia ("minősített licenc") az Azure Virtual machines szolgáltatásban. Az Azure Hybrid Benefittel az SQL Server ügyfelek nem SQL Server-licencét, a virtuális gép használatáért kell fizetnie, de továbbra is a felhőalapú számítási (azaz az alapdíj mellett), storage, és a biztonsági mentéseknek, valamint azok u társított i/o járó költségeket kell fizetnie a szolgáltatások (ha létezik) se.

A Microsoft használati feltételeiben szerint "ügyfeleknek meg kell jelölnie, hogy használják az Azure SQL Database (a felügyelt példánynak, a rugalmas készlet és önálló adatbázisok), az Azure Data Factory, az SQL Server Integration Services vagy az SQL Server virtuális gépek az Azure Hybrid Értékelemek a SQL Server számítási feladatok konfigurálásakor az Azure-ban."

Az SQL Server Azure virtuális gépen az Azure Hybrid Benefit használatát jelzi, és meg kell felelnie, három lehetőség áll rendelkezésre:

1. BYOL SQL Server-lemezkép használatával az Azure piactéren, csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el virtuális gép kiépítése.
1. Az Azure marketplace-ről PAYG SQL Server-lemezkép használatával virtuális gép kiépítése, és aktiválja a AHB.
1. Önálló telepítse kézzel az SQL Server-beli virtuális gépen [regisztrálja az SQL Server rendszerű virtuális gép](virtual-machines-windows-sql-register-with-resource-provider.md) és AHB aktiválásához.

Licenc típusa az SQL Server van beállítva, amikor a virtuális gép ki van építve, és később bármikor módosítható. Licenc modellek közötti váltás tekintetében **állásidő nélkül**, nem indítja újra a virtuális Gépet, hozzáadja **további költségek nélkül** (valójában aktiválása AHB *csökkenti* költség) pedig**azonnali hatállyal**. 

## <a name="prerequisites"></a>Előfeltételek

Az SQL virtuális gép erőforrás-szolgáltató használatához az SQL IaaS-bővítményt. Emiatt a okból rendszerbetöltést végrehajtani az SQL virtuális gép erőforrás-szolgáltató folytatja, a következők szükségesek:
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- [Frissítési garanciával rendelkező](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A [SQL Server rendszerű virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) regisztrálva a [SQL virtuális gép erőforrás-szolgáltató](virtual-machines-windows-sql-register-with-resource-provider.md) telepítve. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>A virtuális gépek már regisztrálva van az erőforrás-szolgáltató módosítása licenc 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Módosíthatja a licencelési modell, közvetlenül a portálról. 

1. Nyissa meg a [az Azure portal](https://portal.azure.com) , majd indítsa el a [SQL virtuális gépek erőforrás](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) az SQL Server virtuális gép. 
1. Válassza ki **konfigurálása** alatt **beállítások**. 
1. Válassza ki a **Azure Hybrid Benefit** lehetőséget, majd jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy van-e frissítési garanciával rendelkező SQL Server-licence. 
1. Válassza ki **alkalmaz** alján a **konfigurálása** lapot. 

![AHB portálon](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Azure CLI segítségével módosíthatja a licencelési modellt.  

A következő kódrészletet a használatalapú licencelési modell átvált BYOL (vagy az Azure Hybrid Benefit használatával):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

A következő kódrészletet a bring-your-saját licenc modell használatalapú fizetéses előfizetésre vált: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Licencelési modelljének módosítása a PowerShell használatával is.

A következő kódrészletet a használatalapú licencelési modell átvált BYOL (vagy az Azure Hybrid Benefit használatával):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

A következő kódrészletet a BYOL modell használatalapú fizetéses előfizetésre vált:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Licenc módosítsa a virtuális gépek erőforrás-szolgáltató nincs regisztrálva

Ha egy SQL Server rendszerű virtuális gép Használatalapú Azure Marketplace rendszerképekből kiépített SQL licenctípus PAYG lesz. BYOL rendszerkép használata az Azure Marketplace-ről az SQL Server virtuális gép üzembe helyezése a licenc típusa AHUB lesz. Az alapértelmezett (Használatalapú) kiépített összes SQL Server virtuális gépen vagy BYOL Azure Marketplace-rendszerképek automatikusan regisztrálva lesz az SQL virtuális gép erőforrás-szolgáltató módosításához a [licenc típusa](#change-license-for-vms-already-registered-with-resource-provider)

Ön csak jogosult helyi telepítése az SQL Server Azure virtuális gépen keresztül az Azure Hybrid Benefit és, érdemes [ezek a virtuális gépek regisztrálása az erőforrás-szolgáltató az SQL virtuális gép](virtual-machines-windows-sql-register-with-resource-provider.md) AHB jelzi a AHB használat szerint, az SQL Server-licencét beállításával A Microsoft használati feltételeiben.

Csak az SQL Server virtuális gépek Használatalapú vagy AHB a licenc típusa esetén módosíthatja az SQL virtuális gép regisztrálva van az SQL virtuális gép erőforrás-szolgáltató; és a licenc megfelelőségének SQL virtuális gép RP kell regisztrálni az összes SQL virtuális gépek.

## <a name="remarks"></a>Megjegyzések

 - Az Azure Cloud Solution Partner (CSP)-ügyfelek először a használatalapú fizetést biztosító virtuális gépek telepítése, majd bring-your-saját licenc, ha rendelkeznek aktív SA szerint képes használni az Azure Hybrid Benefit.
 - Törölte-e az SQL Server VM-erőforrás, állítja vissza a kódolt licenc beállításait, a kép. 
  - Megváltoztathatja a licencelési modell funkciója az SQL virtuális gép erőforrás-szolgáltató. Az erőforrás-szolgáltató SQL Server virtuális gép üzembe helyezése az Azure Portalon keresztül Piactéri lemezképet automatikusan regisztrálja. Azonban ügyfelek, akik saját telepíti az SQL Server rendszer manuálisan kell [regisztrálja az SQL Server rendszerű virtuális gép](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Egy SQL Server rendszerű virtuális gép hozzáadása egy rendelkezésre állási csoportban van szükség a virtuális gép újbóli létrehozását. Mint ilyen, minden olyan virtuális gépeket hozzáadni egy rendelkezésre állási készlet kattintva visszatérhet az alapértelmezett használatalapú licenctípus és AHB újra engedélyezni kell. 


## <a name="limitations"></a>Korlátozások

 - Licencelési modelljének megváltoztatása csak akkor használható, frissítési garanciával rendelkező ügyfelek számára.
 - Licencelési modelljének megváltoztatása csak az SQL Server standard és enterprise kiadásában támogatott. Az Express, a webes és a fejlesztői licenc módosítások nem támogatottak. 
 - Licencelési modelljének megváltoztatása csak a Resource Manager modellel üzembe helyezett virtuális gépek esetén támogatott. A klasszikus modellel üzembe helyezett virtuális gépek nem támogatottak. 
 - Licencelési modelljének megváltoztatása csak engedélyezve van a nyilvános felhőalapú telepítések.
 - Licencelési modelljének megváltoztatása csak virtuális gépek, amelyek egyetlen hálózati adapter (hálózati adapter) támogatott. Az egynél több hálózati Adapterrel rendelkező virtuális gépek, el kell távolítani egy hálózati adapter (az Azure portal használatával) mielőtt elkezdené az eljárást. Ellenkező esetben hiba történt az alábbihoz hasonlóan fog futtatni: `The virtual machine '\<vmname\>' has more than one NIC associated.` Bár előfordulhat, hogy térjen vissza a virtuális gép hozzáadása a hálózati adapter, a licencelési módot módosítása után, az Azure Portalon, az automatikus javítás és a biztonsági mentés, például az SQL konfigurációs oldalán keresztül végzett műveletek fog már nem támogatott.


## <a name="known-errors"></a>Ismert hibák

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Az erőforrás "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<erőforráscsoport->" erőforráscsoportba tartozó "\<erőforráscsoport->" nem található. A "sqlServerLicenseType" tulajdonság nem található ehhez az objektumhoz. Győződjön meg arról, hogy a tulajdonság létezik-e, és akkor állítható be.
Ez a hiba akkor fordul elő, a licencelési modellt, amely az SQL virtuális gép erőforrás-szolgáltató nincs regisztrálva az SQL Server virtuális gépen módosítására tett kísérlet közben. Az erőforrás-szolgáltatót regisztrálnia kell az [előfizetés](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription), majd regisztrálja az SQL Server virtuális gép az SQL-lel [erőforrás-szolgáltató](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>"Sku" paraméter nem lehet érvényesíteni.
Ez a hiba jelentkezhetnek, ha próbál módosítani az SQL Server rendszerű virtuális gép licencelési modellt, ha az Azure PowerShell > 4.0: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Ez a hiba elhárításához távolítsa el ezeket a sorokat a korábban említett PowerShell kódrészletet licencelési modelljének váltáskor:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Az Azure PowerShell-verzió ellenőrzéséhez használja a következő kódot:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Egy Windows virtuális gép az SQL Server használatának áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server használata a Windows virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows virtuális gépek díjszabási útmutatóját](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Az SQL Server használata a Windows virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


