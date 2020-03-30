---
title: SQL Server virtuális gép licencmodelljének módosítása az Azure-ban
description: Megtudhatja, hogy miként válthat az Azure-beli SQL Server virtuális gépek licencelése között a használatalapú fizetésről a saját licencre való áttéréshez az Azure Hybrid Benefit használatával.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201822"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>SQL Server virtuális gép licencmodelljének módosítása az Azure-ban
Ez a cikk azt ismerteti, hogy miként módosíthatja az SQL Server virtuális gépek (VM) licencmodelljét az Azure-ban az új SQL VM erőforrás-szolgáltató, a **Microsoft.SqlVirtualMachine**használatával.

Az SQL Servert üzemeltető virtuális géphez három licencmodell létezik: használatra szánt fizetés, Azure Hybrid Benefit és vészhelyreállítás (DR). Módosíthatja az SQL Server virtuális gép licencmodelljét az Azure Portal, az Azure CLI vagy a PowerShell használatával. 

- A **felosztó-ki-ki-modell** azt jelenti, hogy az Azure virtuális gép futtatásának másodpercenkénti költsége tartalmazza az SQL Server-licenc költségét.
- [Az Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) lehetővé teszi, hogy saját SQL Server-licencet használjon egy SQL Server t futtató virtuális géptel. 
- A **vész-helyreállítási** licenc típus az Azure-ban az [ingyenes VÉSZ-replikához](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) használatos. 

Az Azure Hybrid Benefit lehetővé teszi az SQL Server-licencek használatát frissítési garanciával ("minősített licenc") az Azure virtuális gépeken. Az Azure Hybrid Benefit szolgáltatással az ügyfeleknek nem kell fizetnie az SQL Server-licenc virtuális gépeken való használatáért. De továbbra is fizetniük kell az alapul szolgáló felhőalapú számítás (azaz az alapdíj), a tárolás és a biztonsági mentések költségét. A szolgáltatások használatával kapcsolatos I/O-ért is fizetniük kell (adott esetben).

A Microsoft termékfeltételei szerint: "Az ügyfeleknek jelezniük kell, hogy az Azure SQL Database -t (felügyelt példány, rugalmas készlet és egyetlen adatbázis), az Azure Data Factory-t, az SQL Server Integration Services-t vagy az SQL Server virtuális gépeket használják az Azure-ban Hibrid előny az SQL Server számára az Azure-beli számítási feladatok konfigurálásakor."

Az Azure Hybrid Benefit for SQL Server azure-beli virtuális gépeken való használatának jelzéséhez és a megfelelőséghez három lehetőség közül választhat:

- Virtuális gép kiépítése a bring-your-own-license SQL Server-lemezkép használatával az Azure Marketplace-en. Ez a beállítás csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el.
- Virtuális gép kiépítése használatalapú SQL Server-lemezkép használatával az Azure Marketplace-ről, és aktiválja az Azure Hybrid Benefit.
- Öntelepítő SQL Server egy Azure virtuális gép, manuálisan [regisztráljon az SQL virtuálisgép-erőforrás-szolgáltató](virtual-machines-windows-sql-register-with-resource-provider.md), és aktiválja az Azure Hybrid Benefit.

Az SQL Server licenctípusa a virtuális gép kiépítésekor van beállítva. Utána bármikor megváltoztathatod. A licencmodellek közötti váltás nem jár állásidő, nem indítja újra a virtuális gép vagy az SQL Server szolgáltatás, nem ad hozzá további költségeket, és azonnal lép hatályba. Valójában az Azure Hybrid Benefit *aktiválása csökkenti a költségeket.*

## <a name="prerequisites"></a>Előfeltételek

Az SQL Server virtuális gép licencelési modelljének módosítása a következő követelményekkel rendelkezik: 

- [Egy Azure-előfizetés](https://azure.microsoft.com/free/).
- Sql [Server virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) regisztrálva van az SQL [vm erőforrás-szolgáltatónál.](virtual-machines-windows-sql-register-with-resource-provider.md)
- [A frissítési garancia](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) az Azure Hybrid Benefit kihasználásának [követelménye.](https://azure.microsoft.com/pricing/hybrid-benefit/) 


## <a name="vms-already-registered-with-the-resource-provider"></a>Az erőforrás-szolgáltatónál már regisztrált virtuális gépek 

# <a name="portal"></a>[Portál](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A licencmodellt közvetlenül a portálról módosíthatja: 

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com) és nyissa meg az [SQL-rendszer virtuális gépek erőforrását](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) az SQL Server virtuális géphez. 
1. Válassza a **Beállítások** lehetőséget a **Beállítások csoportban.** 
1. Válassza az **Azure Hybrid Benefit** lehetőséget, és jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy rendelkezik-e FRISSÍTÉSI Garanciával rendelkező SQL Server-licenccel. 
1. Válassza az **Alkalmaz** lehetőséget a **Konfigurálás** lap alján. 

![Az Azure hybrid benefit a portálon](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI segítségével módosíthatja a licencmodellt.  


**Az Azure hibrid előnyei**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Fizessen menet szerint:** 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Vészhelyreállítás (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A PowerShell segítségével módosíthatja a licencmodellt.

**Azure Hybrid Benefit**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Használatalapú fizetés**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Vészhelyreállítás** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Az erőforrás-szolgáltatónál nem regisztrált virtuális gépek

Ha kiépített egy SQL Server virtuális gépet a felosztó-kioldott Azure Marketplace-rendszerképekből, akkor az SQL Server licenctípusa csak akkor lesz, ha csak annyit szeretne fizetni, ahogy van. Ha egy SQL Server virtuális gépet az Azure Marketplace-ről egy bring-your-own-license lemezkép használatával létesített ki, akkor a licenctípusa AHUB lesz. Az alapértelmezett (pay-as-you-go) vagy bring-your-own-license Azure Marketplace-rendszerképek kiépített összes SQL Server virtuális gép automatikusan regisztrálva lesz az SQL virtuálisgép-erőforrás-szolgáltatónál, így módosíthatják a [licenc típusát.](#vms-already-registered-with-the-resource-provider)

Ön csak öntelepítő SQL Server egy Azure virtuális gép az Azure Hybrid Benefit. Ezeket [a virtuális gépeket regisztrálnia kell az SQL virtuálisgép-erőforrás-szolgáltatónál](virtual-machines-windows-sql-register-with-resource-provider.md) az SQL Server licenc Azure Hybrid Benefit beállításával, jelezve az Azure Hybrid Benefit használatát a Microsoft termékfeltételek nek megfelelően.

Az SQL Server virtuális gép licenctípusát csak akkor módosíthatja, ha az SQL Server virtuális gép regisztrálva van az SQL VM erőforrás-szolgáltatónál.

## <a name="remarks"></a>Megjegyzések

- Az Azure Cloud Solution Provider (CSP) ügyfelei használhatják az Azure Hybrid Benefit-et, ha először üzembe helyeznek egy használatalapú virtuális gépre, majd átalakítják saját licencre, ha aktív frissítési garanciával rendelkeznek.
- Ha eldobja az SQL Server virtuálisgép-erőforrást, visszatér a lemezkép kódolható licencbeállításához. 
- A licencmodell módosításának lehetősége az SQL VM erőforrás-szolgáltató egyik jellemzője. Az Azure Marketplace-rendszerkép üzembe helyezése az Azure Portalon keresztül automatikusan regisztrálja az SQL Server virtuális gépet az erőforrás-szolgáltatóval. Az SQL Server t saját telepítésével rendelkező ügyfeleknek azonban manuálisan kell [regisztrálniuk az SQL Server virtuális gépüket.](virtual-machines-windows-sql-register-with-resource-provider.md) 
- Egy SQL Server virtuális gép hozzáadása egy rendelkezésre állási csoporthoz újra létre kell hoznia a virtuális gép. Így az elérhetőségi készlethez hozzáadott virtuális gépek visszaállnak az alapértelmezett felosztó-kioldó licenctípusra. Az Azure Hybrid Benefit-et újra engedélyezni kell. 


## <a name="limitations"></a>Korlátozások

A licencmodell módosítása a következő:
   - Csak a [Frissítési Garanciával](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)rendelkező ügyfelek számára érhető el.
   - Csak az SQL Server Standard és Enterprise kiadásatámogatja. Az Expressz, a Web és a Developer licencmódosításai nem támogatottak. 
   - Csak az Azure Resource Manager modellen keresztül telepített virtuális gépek támogatottak. A klasszikus modellen keresztül telepített virtuális gépek nem támogatottak. 
   - Csak a nyilvános vagy az Azure Government-felhők számára érhető el. 
   - Csak olyan virtuális gépeken támogatott, amelyek egyetlen hálózati adapterrel (NIC) rendelkeznek. 


## <a name="known-errors"></a>Ismert hibák

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>A Forrás "Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>"\<az erőforráscsoport " erőforráscsoport>" alatt nem található.

Ez a hiba akkor fordul elő, ha olyan SQL Server virtuális gép licencmodelljét próbálja módosítani, amely nincs regisztrálva az SQL VM erőforrás-szolgáltatónál:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Regisztrálnia kell az előfizetést az erőforrás-szolgáltatónál, majd [regisztrálnia kell az SQL Server virtuális gépét az erőforrás-szolgáltatónál.](virtual-machines-windows-sql-register-with-resource-provider.md) 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>A virtuális\<gép "\>vmname ' egynél több NIC-hez van társítva

Ez a hiba olyan virtuális gépeken fordul elő, amelyek egynél több hálózati adaptert rendelkeznek. A licencelési modell módosítása előtt távolítsa el az egyik hálózati adaptert. Bár a hálózati adaptert a licencmodell módosítása után visszaveheti a virtuális géphez, az Azure Portalon végzett műveletek, például az automatikus biztonsági mentés és javítás már nem támogatottak. 


## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server rendszerrel kapcsolatos kérdésekről Windows VM rendszeren](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server díjszabási útmutatója Windows virtuális gépen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Kibocsátási megjegyzések az SQL Server rendszerhez Windows vM rendszeren](virtual-machines-windows-sql-server-iaas-release-notes.md)


