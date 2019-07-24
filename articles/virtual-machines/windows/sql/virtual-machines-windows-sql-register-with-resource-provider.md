---
title: Regisztrálja SQL Server virtuális gépet az Azure-ban az SQL VM erőforrás-szolgáltatóval | Microsoft Docs
description: A kezelhetőség javítása érdekében regisztrálja SQL Server VM az SQL VM erőforrás-szolgáltatóval.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305873"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval

Ez a cikk az Azure SQL Server virtuális gép (VM) SQL VM erőforrás-szolgáltatóval való regisztrálását ismerteti. 

SQL Server VM Marketplace lemezképnek a Azure Portal használatával történő üzembe helyezése automatikusan regisztrálja az SQL Server VM az erőforrás-szolgáltatóval. Ha azonban úgy dönt, hogy egy Azure-beli virtuális gépen telepíti SQL Server önálló telepítését ahelyett, hogy az Azure Marketplace-en szeretne kiválasztani egy rendszerképet, akkor a következőt kell SQL Server VM regisztrálnia az erőforrás-szolgáltatónál:

-  **Megfelelőség** – a Microsoft-termékek használati feltételeinek megfelelően a [Azure Hybrid Benefitt](https://azure.microsoft.com/pricing/hybrid-benefit/) használó ügyfeleknek jelezniük kell a Microsoftnak a Azure Hybrid Benefit használatakor – és ha igen, REGISZTRÁLNIUK kell az SQL VM erőforrás-szolgáltatóval. 

-  **Szolgáltatási előnyök** – a SQL Server VM erőforrás-szolgáltatóval való regisztrálása feloldja az [automatikus javítást](virtual-machines-windows-sql-automated-patching.md), az [automatikus biztonsági mentést](virtual-machines-windows-sql-automated-backup-v2.md), a figyelési és kezelhetőségi funkciókat, valamint a [licencelési](virtual-machines-windows-sql-ahb.md) és a [kiadási](virtual-machines-windows-sql-change-edition.md) rugalmasságot. Korábban ezek csak az Azure piactéren SQL Server VM lemezképek számára voltak elérhetők.

Önálló telepítés SQL Server egy Azure-beli virtuális gépen vagy egy Azure-beli virtuális gép üzembe helyezése egy egyéni VHD-vel, Azure Hybrid Benefit SQL Server a SQL Server az SQL VM-erőforrással való regisztrálással a Microsoft számára. Szolgáltató. 

Az SQL-alapú virtuális gép erőforrás-szolgáltatójának használatához regisztrálnia kell az SQL VM erőforrás-szolgáltatót az előfizetésében is. Ez a Azure Portal, az Azure CLI és a PowerShell használatával valósítható meg. 

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM erőforrás-szolgáltatóval való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) és [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Regisztrálás az SQL VM erőforrás-szolgáltatóval
Ha az [SQL IaaS bővítmény](virtual-machines-windows-sql-server-agent-extension.md) már telepítve van a virtuális gépen, akkor az SQL VM erőforrás-szolgáltatóhoz való regisztrálás egyszerűen létrehoz egy Microsoft. SqlVirtualMachine/SqlVirtualMachines típusú metaadat-erőforrást. Az alábbi kódrészlettel regisztrálhat az SQL VM erőforrás-szolgáltatóval, ha az SQL IaaS bővítmény már telepítve van a virtuális gépen. Meg kell adnia SQL Server licenc típusát, ha az SQL VM erőforrás-szolgáltatót "TB" vagy "AHUB" néven regisztrálja. 

Regisztráljon SQL Server VM a PowerShell használatával a következő kódrészlettel:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Ha az SQL IaaS bővítmény nincs telepítve a virtuális gépen, akkor az SQL-alapú virtuális gép erőforrás-szolgáltatójának használatával regisztrálhat az egyszerűsített SQL felügyeleti mód megadásával. Az egyszerűsített SQL Management mode-ban az SQL VM erőforrás-szolgáltatója [egyszerűsített módban](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) telepíti az SQL IaaS bővítményt, és ellenőrzi SQL Server példány metaadatait; Ez nem fog újraindulni SQL Server szolgáltatás. Meg kell adnia SQL Server licenc típusát, ha az SQL VM erőforrás-szolgáltatót "TB" vagy "AHUB" néven regisztrálja. 

Az alábbi kódrészlettel regisztrálja a SQL Server VMt az egyszerű SQL Management módban a PowerShell használatával:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

Az SQL VM erőforrás-szolgáltató [egyszerű módban](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) való regisztrálása biztosítja a megfelelőséget, és lehetővé teszi a rugalmas licencelést, valamint a helyi SQL Server kiadási frissítéseket. A feladatátvevő fürtök példányai és a többpéldányos központi telepítések csak kis-és nagyvállalati módban regisztrálhatók az SQL VM erőforrás-szolgáltatóval. A Azure Portal található útmutatást követve [teljes módba](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) frissíthet, és engedélyezheti az átfogó kezelhetőségi funkciót SQL Server újraindítással bármikor. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>SQL Server 2008/R2 regisztrálása Windows Server 2008 rendszerű virtuális gépeken

A Windows Server 2008 rendszeren telepített 2008-es és 2008 R2-es SQL Server regisztrálható az SQL VM-erőforrással a nem [ügynök](virtual-machines-windows-sql-server-agent-extension.md) módban. Ez a beállítás biztosítja a megfelelőséget, és lehetővé teszi a SQL Server VM figyelését a Azure Portal korlátozott funkcionalitással.

Az alábbi táblázat a regisztráció során megadott paraméterek elfogadható értékeit részletezi:

| Paraméter | Elfogadható értékek                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, vagy`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` vagy `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


A SQL Server 2008-as vagy 2008 R2-es verziónak a Windows Server 2008-példányon való regisztrálásához használja a következő PowerShell-kódrészletet:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Regisztrációs állapot ellenőrzése
A Azure Portal, az Azure CLI vagy a PowerShell használatával ellenőrizheti, hogy a SQL Server már regisztrálva van-e az SQL VM erőforrás-szolgáltatóban. 

### <a name="azure-portal"></a>Azure Portal 
Ha ellenőrizni szeretné a regisztráció állapotát a Azure Portal használatával, tegye a következőket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon az [SQL](virtual-machines-windows-sql-manage-portal.md)-alapú virtuális gépekhez.
1. Válassza ki a SQL Server VM a listából. Ha a SQL Server VM nem szerepel a listán, akkor valószínű, hogy a SQL Server VM nincs regisztrálva az SQL VM erőforrás-szolgáltatóban. 
1. Tekintse meg az `Status`értéket. Ha `Status = Succeeded`a, akkor a SQL Server VM sikeresen regisztrálva lett az SQL VM erőforrás-szolgáltatónál. 

    ![SQL RP-regisztrációval rendelkező állapot ellenőrzése](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az parancssori felület

Ellenőrizze az aktuális SQL Server VM regisztráció állapotát a következő az CLI paranccsal. `ProvisioningState`azt mutatja `Succeeded` , hogy a regisztráció sikeres volt-e. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Ellenőrizze az aktuális SQL Server VM regisztrációjának állapotát a következő PowerShell-parancsmaggal. `ProvisioningState`azt mutatja `Succeeded` , hogy a regisztráció sikeres volt-e. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
A hiba azt jelzi, hogy a SQL Server VM nincs regisztrálva az erőforrás-szolgáltatónál. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>SQL VM erőforrás-szolgáltató regisztrálása az előfizetéssel 

Ha regisztrálni szeretné a SQL Server VM az SQL VM erőforrás-szolgáltatóval, regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében. Ezt megteheti a Azure Portal vagy az Azure CLI használatával.

### <a name="azure-portal"></a>Azure Portal

A következő lépésekkel regisztrálja az SQL VM erőforrás-szolgáltatót az Azure-előfizetésben a Azure Portal használatával. 

1. Nyissa meg a Azure Portalt, és navigáljon az **összes szolgáltatáshoz**. 
1. Navigáljon  az előfizetések elemre, és válassza ki a kamat előfizetését.  
1. Az előfizetések lapon navigáljon az **erőforrás-szolgáltatók**elemre. 
1. Írja `sql` be a szűrőt az SQL-hez kapcsolódó erőforrás-szolgáltatók létrehozásához. 
1. Válassza a **Microsoft. SqlVirtualMachine** -szolgáltató *regisztrálása*, újbóli *regisztrálása*vagy *regisztrációjának* törlése lehetőséget a kívánt művelettől függően. 

   ![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az parancssori felület
A következő kódrészlet regisztrálja az SQL VM erőforrás-szolgáltatót az Azure-előfizetésében. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

A következő PowerShell-kódrészlet regisztrálja az SQL VM erőforrás-szolgáltatót az Azure-előfizetésében.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Megjegyzések

 - Az SQL VM erőforrás-szolgáltató csak a "Resource Manager" használatával telepített SQL Server virtuális gépeket támogatja. SQL Server a klasszikus modell használatával üzembe helyezett virtuális gépek nem támogatottak. 
 - Az SQL VM erőforrás-szolgáltató csak a nyilvános felhőbe telepített SQL Server virtuális gépeket támogatja. A privát vagy kormányzati felhőbe történő központi telepítések nem támogatottak. 
 
## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**Regisztrálhatom a SQL Server VM az Azure Marketplace-en egy SQL-rendszerképből kiépítve?**

Nem. A Microsoft az Azure piactéren az SQL-lemezképből kiépített virtuális gépeket regisztrálja. Az SQL VM erőforrás-szolgáltatóval való regisztrálás csak akkor szükséges, ha a virtuális gép nem lett kiépítve az Azure piactéren található SQL-lemezképből, és a SQL Server önálló telepítése történt.

**Az SQL VM-erőforrásszolgáltató minden ügyfél számára elérhető?** 

Igen. Az ügyfeleknek regisztrálniuk kell az SQL Server VMt az SQL VM erőforrás-szolgáltatóval, ha nem használnak SQL Server rendszerképet az Azure Marketplace-ről, vagy önállóan telepített SQL Server vagy egyéni VHD-t hoztak. Az összes típusú előfizetés (Direct, EA és CSP) tulajdonában lévő virtuális gépek regisztrálhatnak az SQL VM erőforrás-szolgáltatóval.

**Regisztrálni kell az SQL VM erőforrás-szolgáltatónál, ha a SQL Server VM már telepítve van az SQL IaaS bővítmény?**

Ha a SQL Server VM saját telepítésű, és nem az Azure Marketplace-en található SQL-lemezképből lett kiépítve, akkor is regisztrálnia kell az SQL VM erőforrás-szolgáltatóval, még akkor is, ha telepítette az SQL IaaS bővítményt. Az SQL VM erőforrás-szolgáltatóval való regisztrálás egy új, Microsoft. SqlVirtualMachines típusú erőforrást hoz létre. Az SQL IaaS bővítmény telepítése nem hozza létre az erőforrást.

**Mi az alapértelmezett SQL-kezelési mód az SQL VM erőforrás-szolgáltatóval való regisztráláskor?**

Az SQL VM RP-vel való regisztráláskor az alapértelmezett SQL- kezelési mód megtelt. Ha az SQL-alapú virtuális gép erőforrás-szolgáltatójának regisztrálásakor nincs beállítva az SQL Management tulajdonság, a mód teljes Kezelhetőségként lesz beállítva. Ha a virtuális gépen telepítve van az SQL IaaS bővítmény, a teljes körű kezelhetőségi módban regisztrálnia kell az SQL VM erőforrás-szolgáltatót.

**Mik az SQL VM erőforrás-szolgáltatóval való regisztráció előfeltételei?**

Az SQL VM erőforrás-szolgáltatóhoz nem szükséges előfeltétel, hogy egyszerűsített módban vagy nem ügynök módban regisztráljanak. Az SQL VM erőforrás-szolgáltató teljes módban való regisztrálásának előfeltételei az SQL IaaS bővítmény telepítése a virtuális gépre.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval, ha nincs telepítve az SQL IaaS bővítmény a virtuális gépre?**

Igen, ha a virtuális gépen nincs telepítve az SQL IaaS bővítmény, az egyszerűsített felügyeleti módban regisztrálhatja az SQL VM erőforrás-szolgáltatót. A könnyű mód esetében az SQL VM erőforrás-szolgáltatója egy Console-alkalmazást használ az SQL-példány verziójának és kiadásának ellenőrzéséhez. Miután meggyőződött róla, hogy legalább egy SQL-példány fut a virtuális gépen, a konzol-alkalmazás le fog állni. Az SQL VM erőforrás-szolgáltató egyszerű módban való regisztrálása nem fog újraindulni SQL Server, és nem fog ügynököt létrehozni a virtuális gépen.

**Regisztrálja az SQL VM erőforrás-szolgáltatót, telepítsen ügynököt a virtuális gépre?**

Nem. Az SQL VM erőforrás-szolgáltatóval való regisztrálás csak új metaadat-erőforrást hoz létre, és nem telepít ügynököt a virtuális gépre. Az SQL IaaS-bővítmény csak a teljes kezelhetőség engedélyezéséhez szükséges, így a kezelhetőségi mód a könnyűről teljesre való frissítése telepíti az SQL IaaS-bővítményt, és újraindítja SQL Server.

**Regisztrálja az SQL VM erőforrás-szolgáltató újraindítását SQL Server a virtuális gépen?**

Nem. Az SQL VM erőforrás-szolgáltatóval való regisztrálás csak új metaadat-erőforrást hoz létre, és nem indítja újra SQL Server a virtuális gépen. SQL Server újraindítása csak az SQL IaaS bővítmény telepítéséhez szükséges. a teljes kezelhetőség engedélyezéséhez szükség van az SQL IaaS bővítményre. Ha a kezelhetőségi módot könnyűről teljesre frissíti, akkor telepíti az SQL IaaS bővítményt, és újraindítja SQL Server.

**Mi a különbség a könnyű és az ügynök nélküli felügyeleti módok között az SQL VM erőforrás-szolgáltatóval való regisztráláskor?** 

A nem ügynök felügyeleti mód csak a Windows Server 2008-es verziójában érhető el SQL Server 2008/R2 rendszeren; Ez az egyetlen elérhető felügyeleti mód ezen verziók esetében. A SQL Server összes többi verziója esetében az elérhető két kezelhetőségi mód a könnyű és a teljes. A nem ügynök üzemmódhoz az ügyfélnek be kell állítania SQL Server Version és Edition tulajdonságokat. az egyszerűsített mód lekérdezi a virtuális gépet az SQL-példány verziójának és kiadásának megkereséséhez.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatót egyszerű vagy nem ügynök módban az Azure CLI használatával?**

Nem. Az SQL Management Mode tulajdonság csak akkor érhető el, ha az SQL VM erőforrás-szolgáltatót Azure PowerShellsal regisztrálja. Az Azure CLI nem támogatja az SQL kezelhetőségi tulajdonság beállítását, és mindig regisztrálja az SQL VM erőforrás-szolgáltatót az alapértelmezett módban – teljes kezelhetőség.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval az SQL-licenc típusának megadása nélkül?**

Nem. Az SQL-licenc típusa nem választható tulajdonság az SQL VM RP-vel való regisztráláskor. SQL-licencelési típust kell beállítania TB vagy AHUB-ként, ha az SQL VM erőforrás-szolgáltatót minden kezelhetőségi módban (nem ügynök, könnyű és teljes) regisztrálja az az CLI és a PowerShell használatával.

**Frissíthetem az SQL IaaS-bővítményt ügynök nélküli módból teljes módra?**

Nem. Az SQL-kezelhetőségi mód teljes vagy egyszerű frissítése nem érhető el ügynök módban. Ez a Windows Server 2008 technikai korlátozása.

**Frissíthetem az SQL IaaS-bővítményt az egyszerűsített módból a teljes módba?**

Igen. A PowerShell vagy a Azure Portal használatával támogatott az SQL-kezelhetőségi mód frissítése a lightweightről teljesre. és a SQL Server újraindítását igényli.

**Visszaválthatom az SQL IaaS bővítményt teljes módból a nem ügynök vagy az egyszerűsített felügyeleti üzemmódra?**

Nem. Az SQL IaaS-bővítmény kezelhetőségi üzemmódjának lefokozása nem támogatott. Az SQL-kezelhetőségi mód nem állítható vissza teljes módból a könnyű vagy nem ügynök módba; és nem lehet leértékelni az egyszerűsített módból a nem ügynök módba. A kezelhetőségi mód módosítása a teljes kezelhetőséggel; távolítsa el az SQL IaaS bővítményt; dobja el a Microsoft. SqlVirtualMachine erőforrást, és regisztrálja újra SQL Server VM az SQL VM erőforrás-szolgáltatóval.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval Azure Portal?**

Nem. Az SQL VM erőforrás-szolgáltatóval való regisztráció nem érhető el a Azure Portal. Az Azure CLI vagy a PowerShell használatával az SQL VM erőforrás-szolgáltató teljes körű kezelhetőségi módban való regisztrálása támogatott. Ha az SQL VM erőforrás-szolgáltatót könnyű vagy nem ügynöki kezelhetőségi módban regisztrálja, csak Azure PowerShell API-k támogatják.

**Regisztrálhatok egy virtuális gépet az SQL VM erőforrás-szolgáltatóval SQL Server telepítése előtt?**

Nem. A virtuális gépnek legalább egy SQL-példánnyal kell rendelkeznie ahhoz, hogy sikeresen regisztrálja az SQL VM erőforrás-szolgáltatót. Ha nincs SQL-példány a virtuális gépen, akkor az új Microsoft. SqlVirtualMachine erőforrás sikertelen állapotba kerül.

**Regisztrálhatok egy virtuális gépet SQL VM-erőforrással, ha több SQL-példány is van?**

Igen. Az SQL VM erőforrás-szolgáltató csak egy SQL-példányt fog regisztrálni. Az SQL virtuális gép erőforrás-szolgáltatója több példány esetén regisztrálja az alapértelmezett SQL-példányt. Ha nincs alapértelmezett példány, akkor a rendszer csak az egyszerűsített módban történő regisztrációt támogatja. A lightweightről teljes kezelhetőségi üzemmódra való frissítéshez vagy az alapértelmezett SQL-példánynak léteznie kell, vagy a virtuális gépnek csak egy SQL-példánya lehet.

**Regisztrálhatok egy SQL Server feladatátvételi fürtszolgáltatást az SQL VM erőforrás-szolgáltatón keresztül?**

Igen. Az Azure-beli virtuális gépeken futó SQL Server-példányok az SQL VM erőforrás-szolgáltatónál egyszerűsített módban regisztrálhatók. Az SQL Server-példányok azonban nem frissíthetők teljes kezelhetőségi módba.

**Regisztrálhatom a virtuális gépet az SQL VM RP-be, ha az Always ON rendelkezésre állási csoport konfigurálva van?**

Igen. Az SQL VM erőforrás-szolgáltatóval nem lehet SQL Server példányt regisztrálni egy Azure-beli virtuális gépen, ha az Always ON rendelkezésre állási csoport konfigurációjában vesz részt.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows rendszerű virtuális gépen – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server a Windows rendszerű virtuális gépek díjszabási útmutatójában](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server a Windows rendszerű virtuális gépek kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)
