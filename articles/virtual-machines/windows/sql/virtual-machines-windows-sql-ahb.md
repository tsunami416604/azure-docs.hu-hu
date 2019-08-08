---
title: Az Azure-beli SQL Server VM licencelési modelljének módosítása
description: Megtudhatja, hogyan válthat az Azure-beli SQL-alapú virtuális gépek licencelése a "Pay-as-you-go" és a "saját licenc" között a Azure Hybrid Benefit használatával.
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
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816717"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>SQL Server virtuális gép licencelési modelljének módosítása az Azure-ban
Ez a cikk azt ismerteti, hogyan változtatható meg egy SQL Server virtuális gép licencelési modellje az Azure-ban az új SQL VM erőforrás-szolgáltató – **Microsoft. SqlVirtualMachine**használatával.

Két licencelési modell létezik egy virtuális gép (VM) számára, SQL Server utólagos elszámolású, és Azure Hybrid Benefit (AHB). Mostantól a Azure Portal, az Azure CLI vagy a PowerShell használatával módosíthatja a SQL Server VM licencelési modelljét. 

Az utólagos elszámolású (TB) modell azt jelenti, hogy az Azure-beli virtuális gép futtatásának másodpercenkénti díja magában foglalja a SQL Server licenc költségeit.
A [Azure Hybrid Benefit (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) lehetővé teszi, hogy a saját SQL Server licencét egy SQL Server futtató virtuális géppel használhassa. 

Microsoft Azure Hybrid Benefit for SQL Server lehetővé teszi SQL Server licencek használatát az Azure Virtual Machines frissítési garanciával ("minősített licenccel"). A SQL Server Azure Hybrid Benefit esetében az ügyfeleket nem terheli a virtuális gép SQL Server licencének használata, de a mögöttes Felhőbeli számítás (azaz az alaparány), a tárterület és a biztonsági másolatok, valamint az u/O-vel társított költségeket is fizetniük kell. a szolgáltatások se-je (adott esetben).

A Microsoft-termékek használati feltételei szerint a felhasználóknak jelezniük kell, hogy a Azure SQL Database (felügyelt példány, rugalmas készlet és önálló adatbázis), Azure Data Factory, SQL Server Integration Services vagy SQL Server Azure Hybrid A számítási feladatok Azure-ban való konfigurálásakor SQL Server juttatás. "

Ha az Azure-beli virtuális gépen SQL Server Azure Hybrid Benefit használatát szeretné jelezni, és meg kell felelnie az előírásoknak, három lehetőség közül választhat:

1. Hozzon létre egy virtuális gépet egy BYOL SQL Server rendszerképpel az Azure Marketplace-ről, csak Nagyvállalati Szerződés-ügyfelek számára érhető el.
1. Hozzon létre egy virtuális gépet egy TB SQL Server rendszerképpel az Azure Marketplace-ről, és aktiválja a AHB.
1. Önálló telepítés SQL Server egy Azure-beli virtuális gépen, manuálisan [regisztrálja SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md) és aktiválja a AHB.

A SQL Server licencének beállítása a virtuális gép üzembe helyezésekor történik, és később bármikor módosítható. A licencelési modellek közötti váltásnem eredményez leállást, a nem indítja újra a virtuális gépet, és nem **jár további költségekkel** (valójában a AHB aktiválása *csökkenti* a költségeket), és **azonnal érvénybe lép**. 

## <a name="prerequisites"></a>Előfeltételek

Az SQL virtuális gép erőforrás-szolgáltatójának használatához az SQL IaaS bővítmény szükséges. Így az SQL virtuális gép erőforrás-szolgáltatójának kihasználása érdekében a következőkre lesz szüksége:
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Frissítési [garancia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- A telepített [SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatónál regisztrált [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) . 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Az erőforrás-szolgáltatónál már regisztrált virtuális gépek licencének módosítása 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A licencelési modellt közvetlenül a portálról is módosíthatja. 

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és indítsa el az [SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) -erőforrást a SQL Server VMhoz. 
1. Válassza a **Konfigurálás** lehetőséget a **Beállítások**területen. 
1. Válassza a **Azure Hybrid Benefit** lehetőséget, és jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy rendelkezik-e frissítési garanciával rendelkező SQL Server licenccel. 
1. Válassza az **alkalmaz** lehetőséget a configure ( **Konfigurálás** ) lap alján. 

![AHB a portálon](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ PARANCSSORI FELÜLET](#tab/bash)

Az Azure CLI használatával módosíthatja a licencelési modellt.  

A következő kódrészlet bekapcsolja az utólagos elszámolású BYOL (vagy a Azure Hybrid Benefit használatával):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Az alábbi kódrészlet a saját licences modellre vált az utólagos elszámolású modellre: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
A PowerShellt használhatja a licencelési modell módosításához.

A következő kódrészlet bekapcsolja az utólagos elszámolású BYOL (vagy a Azure Hybrid Benefit használatával):

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

A következő kódrészlet a BYOL-modellt az utólagos elszámolású üzemmódra vált:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Az erőforrás-szolgáltatóval nem regisztrált virtuális gépek licencének módosítása

Ha kiépített egy SQL Server VMt az Azure Marketplace TB, akkor az SQL-licenc típusa TB lesz. Ha kiépített egy SQL Server VM az Azure Marketplace-ről származó BYOL-rendszerkép használatával, akkor a licenc típusa AHUB lesz. Az alapértelmezett (TB) vagy BYOL Azure Marketplace-lemezképből kiépített összes SQL Server virtuális gép automatikusan regisztrálva lesz az SQL VM erőforrás-szolgáltatóban, így módosíthatja a [licenc típusát](#change-license-for-vms-already-registered-with-resource-provider)

Csak az Azure-beli virtuális gépeken Azure Hybrid Benefit-on keresztül telepíthető SQL Server, és ezeket a virtuális gépeket az [SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatóval kell regisztrálnia úgy, hogy az SQL Server-licencet AHB adja, hogy a AHB a Microsoft-termékek használati feltételeinek megfelelően határozza meg.

Ha az SQL-alapú virtuális gép SQL VM erőforrás-szolgáltatóval van regisztrálva, csak TB vagy AHB típusú SQL Server VM lehet módosítani. és minden SQL virtuális gépet regisztrálni kell az SQL VM RP-ben a licencek megfelelősége érdekében.

## <a name="remarks"></a>Megjegyzések

 - Az Azure Cloud Solution partner (CSP) ügyfelei az utólagos elszámolású virtuális gépek üzembe helyezésével, majd a saját licenc használatára való átalakításával használhatják a Azure Hybrid Benefit.
 - Ha eldobja a SQL Server VM-erőforrást, visszatérhet a rendszerkép rögzített licencelési beállítására. 
  - A licencelési modell megváltoztatásának lehetősége az SQL VM erőforrás-szolgáltató szolgáltatása. A Piactéri rendszerképeknek a Azure Portal használatával történő üzembe helyezése automatikusan regisztrálja SQL Server VM az erőforrás-szolgáltatóval. A SQL Server önálló telepítését végző ügyfeleknek azonban manuálisan kell [regisztrálniuk SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md). 
- SQL Server VM hozzáadása egy rendelkezésre állási csoporthoz újra kell létrehozni a virtuális gépet. Így a rendelkezésre állási csoportba felvett virtuális gépek visszatérhetnek az alapértelmezett utólagos elszámolású licenccel, és a AHB újra engedélyezni kell. 


## <a name="limitations"></a>Korlátozások

 - A licencelési modell módosítása csak a frissítési garanciával rendelkező ügyfelek számára érhető el.
 - A licencelési modell módosítása csak a SQL Server standard és Enterprise kiadásában támogatott. Az Express, a web és a Developer licencelési módosításai nem támogatottak. 
 - A licencelési modell módosítása csak a Resource Manager-modell használatával üzembe helyezett virtuális gépek esetében támogatott. A klasszikus modell használatával üzembe helyezett virtuális gépek nem támogatottak. A virtuális gépet a klasszikusról Resource Manager-(ARM-) modellre telepítheti át, és regisztrálhatja az SQL VM erőforrás-szolgáltatót. Ha a virtuális gép regisztrálva van az SQL VM erőforrás-szolgáltatónál, a licencelési modell módosításai elérhetők lesznek a virtuális gépen. 
 - A licencelési modell módosítása csak a nyilvános Felhőbeli telepítésekhez engedélyezett.
 - A licencelési modell módosítása csak egyetlen hálózati adapterrel rendelkező virtuális gépeken támogatott. Az egynél több hálózati adapterrel rendelkező virtuális gépeken először távolítsa el az egyik hálózati adaptert (a Azure Portal használatával) az eljárás megkísérlése előtt. Ellenkező esetben a következőhöz hasonló hibaüzenetet fog futtatni: `The virtual machine '\<vmname\>' has more than one NIC associated.`Bár lehetséges, hogy a hálózati adaptert vissza tudja adni a virtuális géphez a licencelési mód módosítása után, a Azure Portal SQL-konfiguráció lapján végrehajtott műveletek, például az automatikus javítás és a biztonsági mentés, a továbbiakban nem lesznek támogatottak.


## <a name="known-errors"></a>Ismert hibák

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>Nem található a (z) "Microsoft.\<SqlVirtualMachine/SqlVirtualMachines/Resource-Group >"\<erőforrás a (z) "erőforrás-csoport >" erőforráscsoport alatt. A "sqlServerLicenseType" tulajdonság nem található ezen az objektumon. Ellenőrizze, hogy létezik-e a tulajdonság, és beállítható-e.
Ez a hiba akkor fordul elő, ha olyan SQL Server VM licencelési modelljét kísérli meg módosítani, amely nem lett regisztrálva az SQL VM erőforrás-szolgáltatóban. Regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében, majd regisztrálnia kell a SQL Server VM az SQL [erőforrás-szolgáltatóval](virtual-machines-windows-sql-register-with-resource-provider.md). [](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription) 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Az "SKU" paraméter argumentuma nem ellenőrizhető
Ez a hiba akkor fordulhat elő, ha a SQL Server VM licencelési modelljét Azure PowerShell > 4,0 használatakor próbálja meg módosítani:`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

A hiba megoldásához szüntesse meg a következő, a korábban említett PowerShell-kódrészletben szereplő sorok megjegyzéseit a licencelési modell váltásakor:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
A Azure PowerShell verziójának ellenőrzéséhez használja a következő kódot:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows rendszerű virtuális gépen – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows rendszerű virtuális gépek díjszabási útmutatójában](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server a Windows rendszerű virtuális gépek kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


