---
title: Az Azure-beli SQL Server VMhoz tartozó licencelési modell módosítása
description: Megtudhatja, hogyan válthat az Azure-beli SQL Server virtuális gépek licencelése a saját licencek megvásárlásával a Azure Hybrid Benefit használatával.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2cce369f6a0670790ede7367609f87c18672ddd5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100614"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>SQL Server virtuális gép licencelési modelljének módosítása az Azure-ban
Ez a cikk azt ismerteti, hogyan változtatható meg egy SQL Server virtuális gép (VM) licencelési modellje az Azure-ban az új SQL VM erőforrás-szolgáltató, a **Microsoft. SqlVirtualMachine**használatával.

A SQL Server üzemeltető virtuális gép két licenccel rendelkezik: utólagos elszámolású és Azure Hybrid Benefit. A SQL Server VM licencelési modelljét a Azure Portal, az Azure CLI vagy a PowerShell használatával módosíthatja. 

Az utólagos elszámolású modell azt jelenti, hogy az Azure-beli virtuális gép futtatásának másodpercenkénti díja magában foglalja a SQL Server licenc költségeit.
[Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) lehetővé teszi, hogy a saját SQL Server licencét egy SQL Server futtató virtuális géppel használhassa. 

Azure Hybrid Benefit lehetővé teszi a frissítési garanciával rendelkező SQL Server-licencek használatát az Azure-beli virtuális gépeken. A Azure Hybrid Benefit használata esetén az ügyfelek nem számítanak fel díjat a virtuális gépeken SQL Server licencek esetében. Azonban továbbra is fizetniük kell a mögöttes Felhőbeli számítás (azaz az alaparány), a tárterület és a biztonsági másolatok költségeiért. Emellett a szolgáltatások használatára vonatkozó I/O-díjat is fizetniük kell (ha vannak ilyenek).

A Microsoft-termékek feltételeinek megfelelően: "A felhasználóknak jelezniük kell, hogy a Azure SQL Database (felügyelt példány, rugalmas készlet és önálló adatbázis), Azure Data Factory, SQL Server Integration Services vagy SQL Server Virtual Machines a konfigurálásakor Azure Hybrid Benefit SQL Server munkaterhelések az Azure-ban. "

Ha az Azure-beli virtuális gépen SQL Server Azure Hybrid Benefit használatát szeretné jelezni, és a megfelelőnek kell lennie, három lehetősége van:

- Hozzon létre egy virtuális gépet egy saját licencű SQL Server rendszerkép használatával az Azure Marketplace-en. Ez a beállítás csak Nagyvállalati Szerződés rendelkező ügyfelek számára érhető el.
- Hozzon létre egy virtuális gépet az Azure Marketplace-en az utólagos elszámolású SQL Server rendszerképpel, és aktiválja Azure Hybrid Benefit.
- Önálló telepítés SQL Server Azure-beli virtuális gépen, manuálisan [regisztrálja a SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md), és aktiválja Azure Hybrid Benefit.

A SQL Server licencének típusa a virtuális gép üzembe helyezésekor van beállítva. Később bármikor megváltoztathatja. A licencelési modellek közötti váltás nem eredményez állásidőt, a nem indítja újra a virtuális gépet, és nem jár további költségekkel, és azonnal érvénybe lép. Valójában a Azure Hybrid Benefit aktiválása *csökkenti* a költségeket.

## <a name="prerequisites"></a>Előfeltételek

Az SQL virtuális gép erőforrás-szolgáltatójának használatához a SQL Server IaaS bővítmény szükséges. Ennek megfelelően a következőkre lesz szüksége:
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Frissítési [garancia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Az [SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md)-szolgáltatónál regisztrált [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) .


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Az erőforrás-szolgáltatónál már regisztrált virtuális gépek licencének módosítása 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A licencelési modellt közvetlenül a portálról is módosíthatja: 

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és nyissa meg az [SQL Virtual Machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) -erőforrást a SQL Server VMhoz. 
1. Válassza a **Konfigurálás** lehetőséget a **Beállítások**területen. 
1. Válassza a **Azure Hybrid Benefit** lehetőséget, majd jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy rendelkezik-e a frissítési garanciával rendelkező SQL Server licenccel. 
1. Válassza az **alkalmaz** lehetőséget a configure ( **Konfigurálás** ) lap alján. 

![Azure Hybrid Benefit a portálon](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatával módosíthatja a licenc modelljét.  

A következő kódrészlet bekapcsolja az utólagos elszámolású licencelési modellt a saját licenc használatára (vagy a Azure Hybrid Benefit használatával):

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
A PowerShell segítségével módosíthatja a licenc modelljét.

A következő kódrészlet bekapcsolja az utólagos elszámolású licencelési modellt a saját licenc használatára (vagy a Azure Hybrid Benefit használatával):

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

Az alábbi kódrészlet a saját licences modellre vált az utólagos elszámolású modellre:

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

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Az erőforrás-szolgáltatónál nem regisztrált virtuális gépek licencének módosítása

Ha kiépített egy SQL Server VM az utólagos elszámolású Azure Marketplace-rendszerképekből, akkor a SQL Server licenc típusa utólagos elszámolású lesz. Ha az Azure Marketplace-en keresztül létrehozott egy saját licenctel ellátott rendszerképet használó SQL Server VM-t, a licenc típusa AHUB lesz. Az alapértelmezett (utólagos elszámolású) vagy a saját licencű Azure Marketplace-lemezképek által kiépített SQL Server virtuális gépek automatikusan regisztrálva lesznek az SQL VM erőforrás-szolgáltatóban, így megváltoztathatják a [licenc típusát](#change-the-license-for-vms-already-registered-with-the-resource-provider).

Csak az Azure-beli virtuális gépeken Azure Hybrid Benefit-n keresztül telepíthet SQL Server. Ezeket a [virtuális gépeket az SQL VM erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md) -szolgáltatóval kell regisztrálnia, ha a SQL Server-licencet Azure Hybrid Benefitként állítja be, hogy jelezze a Azure Hybrid Benefit használatát a Microsoft termék feltételeinek megfelelően.

A SQL Server VM licencének típusa csak akkor módosítható, ha a SQL Server VM regisztrálva van az SQL VM erőforrás Azure Hybrid Benefit-szolgáltatóban. Minden SQL Server virtuális gépet regisztrálni kell az erőforrás-szolgáltatónál a licencek megfelelősége érdekében.

## <a name="remarks"></a>Megjegyzések

- A Azure Cloud Solution Provider (CSP) ügyfelek a Azure Hybrid Benefit az utólagos elszámolású virtuális gépek üzembe helyezésével, majd a saját licenc használatára való átalakításával, ha aktív frissítési garanciával rendelkeznek.
- Ha eldobja a SQL Server VM-erőforrást, visszatérhet a rendszerkép rögzített licencelési beállítására. 
- A licencelési modell megváltoztatásának lehetősége az SQL VM erőforrás-szolgáltató szolgáltatása. Egy Azure Marketplace-rendszerkép üzembe helyezése a Azure Portal automatikusan regisztrálja az SQL Server VM az erőforrás-szolgáltatóval. A SQL Server önálló telepítését végző ügyfeleknek azonban manuálisan kell [regisztrálniuk SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md). 
- SQL Server VM hozzáadása egy rendelkezésre állási csoporthoz újra kell létrehozni a virtuális gépet. Így a rendelkezésre állási csoportba felvett virtuális gépek visszatérhetnek az alapértelmezett utólagos elszámolású licenc típusára. Azure Hybrid Benefit újra engedélyezni kell. 


## <a name="limitations"></a>Korlátozások

- A licencelési modell módosítása csak a frissítési garanciával rendelkező ügyfelek számára érhető el.
- A licencelési modell módosítása csak a SQL Server standard és Enterprise kiadásában támogatott. Az Express, a web és a Developer licencelési módosításai nem támogatottak. 
- A licencelési modell módosítása csak a Azure Resource Manager modellen keresztül üzembe helyezett virtuális gépek esetén támogatott. A klasszikus modellen keresztül üzembe helyezett virtuális gépek nem támogatottak. Telepítheti a virtuális gépet a Klasszikusból a Resource Manager-modellbe, és regisztrálhatja az SQL VM erőforrás-szolgáltatóval. Miután a virtuális gép regisztrálva lett az SQL VM erőforrás-szolgáltatónál, a licenc-modell módosításai elérhetők lesznek a virtuális gépen.
- A licencelési modell módosítása csak a nyilvános Felhőbeli telepítésekhez engedélyezett.
- A licencelési modell módosítása csak olyan virtuális gépeken támogatott, amelyek egyetlen hálózati adapterrel rendelkeznek. Az egynél több hálózati adapterrel rendelkező virtuális gépeken először távolítsa el az egyik hálózati adaptert (a Azure Portal használatával) az eljárás megkísérlése előtt. Ellenkező esetben a következőhöz hasonló hibaüzenet jelenik meg: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  Bár lehetséges, hogy a hálózati adaptert újra hozzá szeretné adni a virtuális géphez a licencelési modell módosítása után, a Azure Portal SQL Server konfiguráció lapján végrehajtott műveletek, például az automatikus javítás és a biztonsági mentés, a továbbiakban nem lesznek támogatottak.

## <a name="known-errors"></a>Ismert hibák

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Nem található a (z) "Microsoft.\<SqlVirtualMachine/SqlVirtualMachines/Resource-Group >"\<erőforrás a (z) "erőforrás-csoport >" erőforráscsoport alatt.
Ez a hiba akkor fordul elő, ha olyan SQL Server VMon próbálja meg módosítani a licencet, amely még nincs regisztrálva az SQL VM erőforrás-szolgáltatónál:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében, majd regisztrálnia kell a [SQL Server VM az erőforrás](virtual-machines-windows-sql-register-with-resource-provider.md)-szolgáltatóval. [](virtual-machines-windows-sql-register-with-resource-provider.md#register-the-sql-vm-resource-provider-with-a-subscription) 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Az "SKU" paraméter argumentuma nem ellenőrizhető
Ez a hiba akkor fordulhat elő, amikor a 4,0-nál újabb Azure PowerShell-verziókkal próbálja meg módosítani a SQL Server VM licenc modelljét:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Ennek a hibának a megoldásához szüntesse meg az előzőleg említett PowerShell-kódrészlet megjegyzéseit a licencelési modell váltásakor:

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
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)


