---
title: Regisztráció az SQL Virtuálisgép-erőforrás-szolgáltatóval
description: Regisztrálja az Azure SQL Server virtuális gépét az SQL VM-erőforrás-szolgáltatóval, és engedélyezze az Azure Marketplace-en kívül telepített SQL Server virtuális gépek szolgáltatásait, valamint a megfelelőséget és a jobb kezelhetőséget.
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 01e683e31905281d25fdcf976bc58397c052a6c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243184"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>SQL Server virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval

Ez a cikk ismerteti, hogyan regisztrálhatja az SQL Server virtuális gép (VM) az Azure-ban az SQL VM erőforrás-szolgáltató. Az erőforrás-szolgáltatóval való regisztráció létrehozza az SQL _virtuálisgép-erőforrást_ az előfizetésen belül, amely egy külön erőforrás a virtuálisgép-erőforrástól. **SQL virtual machine** Ha törli az SQL Server virtuális gép regisztrációját az erőforrásszolgáltatóból, az eltávolítja az SQL _virtuálisgép-erőforrást,_ de nem dobja el a tényleges virtuális gépet. **SQL virtual machine** 

Az SQL Server VM Azure Marketplace-lemezkép telepítése az Azure Portalon keresztül automatikusan regisztrálja az SQL Server virtuális gépet az erőforrás-szolgáltatóval. Ha azonban úgy dönt, hogy öntelepítő SQL Server egy Azure virtuális gépen, vagy egy Azure virtuális gép kiépítése egy egyéni virtuális merevlemez, regisztrálnia kell az SQL Server virtuális gép az erőforrás-szolgáltató:

- **Funkcióelőnyök:** Az SQL Server virtuális gép regisztrálása az erőforrás-szolgáltatóval feloldja az [automatikus javítást](virtual-machines-windows-sql-automated-patching.md), [az automatikus biztonsági mentést,](virtual-machines-windows-sql-automated-backup-v2.md)valamint a figyelési és felügyeleti képességeket. Ez is kinyit [engedélyező](virtual-machines-windows-sql-ahb.md) és [kiadás](virtual-machines-windows-sql-change-edition.md) hajlékonyság. Korábban ezek a funkciók csak az Azure Marketplace-ről telepített SQL Server virtuálisgép-lemezképek számára voltak elérhetők. 

- **Megfelelőség:** Az SQL VM erőforrás-szolgáltatóval történő regisztráció egyszerűsített módszert kínál a Microsoft értesítésére vonatkozó követelmény teljesítésére, hogy az Azure Hybrid Benefit engedélyezve van a termékfeltételekben meghatározottak szerint. Ez a folyamat tagadja, hogy minden egyes erőforráshoz licencregisztrációs űrlapokat kellene kezelni.  

- **Ingyenes felügyelet:** Az SQL VM erőforrás-szolgáltatóval való regisztráció mindhárom kezelhetőségi módban teljesen ingyenes. Az erőforrás-szolgáltatóhoz vagy a felügyeleti módok módosításához nincs további költség. 

- **Egyszerűsített licenckezelés:** Az SQL VM-erőforrás-szolgáltatóval való regisztráció leegyszerűsíti az SQL Server licenckezelését, és lehetővé teszi az SQL Server virtuális gépek gyors azonosítását az Azure Hybrid Benefit használatával az [Azure Portalon,](virtual-machines-windows-sql-manage-portal.md)az Az CLI-ben vagy a PowerShellben engedélyezett Azure Hybrid Benefit használatával: 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Az SQL Virtuálisgép-erőforrás-szolgáltató használatához először regisztrálnia kell [az előfizetést az erőforrás-szolgáltatónál,](#register-subscription-with-rp)amely lehetővé teszi az erőforrás-szolgáltató számára, hogy erőforrásokat hozzon létre az adott előfizetésen belül.

Az SQL VM erőforrás-szolgáltató használatának előnyeiről a [channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) videóban talál további információt: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Használja ki az SQL VM erőforrás-szolgáltató előnyeit az SQL Server öntelepítése során az Azure-ban – Microsoft Channel 9 Video"></iframe>


## <a name="prerequisites"></a>Előfeltételek

Az SQL Server virtuális gép regisztrálásához az erőforrás-szolgáltatónál a következőkre lesz szükség: 

- [Egy Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy Azure Resource Model [SQL Server virtuális gép](virtual-machines-windows-portal-sql-server-provision.md) a nyilvános vagy az Azure Government felhőben üzembe helyezett. 
- Az Azure [CLI](/cli/azure/install-azure-cli) vagy a [PowerShell](/powershell/azure/new-azureps-module-az)legújabb verziója. 

## <a name="management-modes"></a>Kezelési módok

Ha az [SQL IaaS-bővítmény](virtual-machines-windows-sql-server-agent-extension.md) még nincs telepítve, az SQL Virtuálisgép-erőforrás-szolgáltatóval való regisztráció automatikusan telepíti az SQL Server IaaS-bővítményt a regisztrációs folyamat során megadott három felügyeleti mód egyikében. Ha nem adja meg a felügyeleti módot, akkor az SQL IaaS bővítmény teljes felügyeleti módban települ.  

Ha az SQL IaaS-bővítmény már manuálisan telepítve van, akkor már teljes felügyeleti módban van, és az erőforrás-szolgáltatóval való regisztráció teljes módban nem indítja újra az SQL Server szolgáltatást.

A három kezelési mód a következő:

- **A könnyű** mód nem igényli az SQL Server újraindítását, de csak az SQL Server licenctípusának és kiadásának módosítását támogatja. Ezt a beállítást több példánysal rendelkező VAGY feladatátvevő fürtpéldányban (FCI) részt vevő SQL Server-virtuális gépekesetén használja. A könnyű mód használatakor nincs hatással a memóriára vagy a PROCESSZORra, és nincs kapcsolódó költség. Javasoljuk, hogy először regisztrálja az SQL Server virtuális gépet könnyű üzemmódban, majd frissítsen teljes módra egy ütemezett karbantartási időszakban.  

- **A teljes** mód minden funkciót biztosít, de újra kell indítani az SQL Server és a rendszergazdai engedélyeket. Ez az a beállítás, amely alapértelmezés szerint telepítve van az SQL IaaS bővítmény manuális telepítésekor. Az SQL Server virtuális gép egyetlen példánysal történő kezeléséhez használható. A teljes mód két olyan Windows-szolgáltatást telepít, amelyek minimális hatást gyakorolnak a memóriára és a CPU-ra - ezek a feladatkezelőn keresztül figyelhetők meg. A teljes kezelhetőségi mód használata nem jár költséggel. 

- **A NoAgent** mód a Windows Server 2008 rendszeren telepített SQL Server 2008 és SQL Server 2008 R2 rendszerhez készült. NoAgent mód használata kor nincs hatással a memóriára vagy a processzorra. A NoAgent kezelhetőségi mód használatával kapcsolatban nincs költség. 

Az SQL Server IaaS-ügynök aktuális módját a PowerShell használatával tekintheti meg: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Előfizetés regisztrálása RP-vel

Az SQL Server virtuális gép regisztrálásához az SQL vm erőforrás-szolgáltató, először regisztrálnia kell az előfizetést az erőforrás-szolgáltatónál. Ez lehetővé teszi az SQL virtuálisgép-erőforrás-szolgáltató számára, hogy erőforrásokat hozzon létre az előfizetésen belül.  Ezt az Azure Portal, az Azure CLI vagy a PowerShell használatával teheti meg.

### <a name="azure-portal"></a>Azure portál

1. Nyissa meg az Azure-portált, és nyissa meg a **Minden szolgáltatás című kaput.** 
1. Nyissa meg az **Előfizetések** lehetőséget, és válassza ki az érdeklődési áget.  
1. Az **Előfizetések** lapon nyissa meg **az Erőforrás-szolgáltatók (Resource providers) (Erőforrás-szolgáltatók)** lapot. 
1. Írja be az **sql-t** a szűrőbe az SQL-hez kapcsolódó erőforrás-szolgáltatók hozásához. 
1. Válassza **a Regisztráció**, **Újraregisztrálás**vagy A Regisztráció megszüntetése **lehetőséget** a **Microsoft.SqlVirtualMachine** szolgáltatóhoz a kívánt művelettől függően. 

![A szolgáltató módosítása](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Parancssor

Regisztrálja az SQL VM-erőforrás-szolgáltatót az Azure-előfizetésbe az Az CLI vagy a PowerShell használatával. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>SQL virtuális gép regisztrálása RP-vel 

### <a name="lightweight-management-mode"></a>Könnyű felügyeleti mód

Ha az [SQL Server IaaS Ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md) nincs telepítve a virtuális gépre, majd a javaslat az, hogy regisztráljon az SQL Virtuálisgép erőforrás-szolgáltató könnyű módban. Ez [könnyű módban](#management-modes) telepíti az SQL IaaS-bővítményt, és megakadályozza az SQL Server szolgáltatás újraindítását. Ezután bármikor frissíthet teljes módba, de ezúttal újraindítja az SQL Server szolgáltatást, ezért ajánlott megvárni az ütemezett karbantartási időszakot. 

Sql Server licenctípus biztosítása használatonként i-használat (`PAYG`) használatonkénti`AHUB`fizetéshez, azure hybrid benefit`DR`( ) a saját licenc használatához, vagy a vészhelyreállítás ( ) az [ingyenes DR replikalicenc](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)aktiválásához .

Feladatátvevő fürtpéldányok és többpéldányos központi telepítések csak az SQL virtuálisgép-erőforrás-szolgáltató nal regisztrálható könnyű módban. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Regisztrálja az SQL Server virtuális gépet könnyű üzemmódban az Az CLI-vel: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[Powershell](#tab/powershell)

Regisztrálja az SQL Server virtuális gépet könnyű üzemmódban a PowerShell használatával:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Teljes felügyeleti mód


Ha az SQL IaaS-bővítmény már telepítve van a virtuális gép manuálisan, majd regisztrálhatja az SQL Server virtuális gép teljes módban újraindítása nélkül. **Ha azonban az SQL IaaS-bővítmény nincs telepítve, a teljes módban történő regisztráció teljes módban telepíti az SQL IaaS bővítményt teljes módban, és újraindítja az SQL Server szolgáltatást. Kérjük, óvatosan járjon el.**


Az SQL Server virtuális gép közvetlen regisztrálásához teljes módban (és esetleg az SQL Server szolgáltatás újraindításához) használja a következő PowerShell parancsot: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent felügyeleti mód 

A Windows Server 2008 rendszerre telepített SQL Server 2008 és 2008 R2 (_nem R2_) regisztrálható az SQL VM erőforrás-szolgáltatónál [NoAgent módban](#management-modes). Ez a beállítás biztosítja a megfelelőséget, és lehetővé teszi, hogy az SQL Server virtuális gép korlátozott funkcionalitással az Azure Portalon legyen figyelve.

Adja `AHUB`meg `PAYG`a `DR` , vagy **sqlLicenseType** `SQL2008R2-WS2008` értéket, illetve az `SQL2008-WS2008` **sqlImageOffer**értéket. 

Az SQL Server 2008 vagy 2008 R2 példány Windows Server 2008 példányon történő regisztrálásához használja a következő Az CLI- vagy PowerShell-kódrészletet: 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Regisztrálja az SQL Server 2008 virtuális gépet NoAgent módban az Az CLI-vel: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```
 
 
Regisztrálja az SQL Server 2008 R2 virtuális gépet NoAgent módban az Az CLI-vel: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008R2
 ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Regisztrálja az SQL Server 2008 virtuális gépet NoAgent módban a PowerShell használatával: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Regisztrálja az SQL Server 2008 R2 virtuális gépet NoAgent módban a PowerShell használatával: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Frissítés teljes felügyeleti módra 

Sql Server virtuális gépek, amelyek a *könnyű* IaaS-bővítmény telepítve van a mód teljes az Azure Portalon, az Az CLI vagy a PowerShell _használatával._ _A NoAgent_ módban futó SQL Server virtuális gépek az operációs rendszer Windows 2008 R2 vagy újabb rendszerre történő frissítése után _teljes_ rekedhetnek. Nem lehet visszaminősíteni - ehhez törölnie kell az SQL Server virtuális gép [regisztrációját](#unregister-vm-from-rp) az SQL VM erőforrás-szolgáltatóról. Ezzel eltávolítja az **SQL virtuálisgép-erőforrást,** _resource_de nem törli a tényleges virtuális gépet. 

Az SQL Server IaaS-ügynök aktuális módját a PowerShell használatával tekintheti meg: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Az ügynökmód teljes re való frissítése: 


### <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Nyissa meg az [SQL virtuális gépek](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) erőforrását. 
1. Válassza ki az SQL Server virtuális gépet, és válassza **az Áttekintés**lehetőséget. 
1. A NoAgent vagy könnyű IaaS-móddal rendelkező SQL Server virtuális gépek esetén válassza ki a **Csak licenctípus t és a kiadásfrissítéseit az SQL IaaS bővítményüzenettel.**

   ![A mód nak a portálról való módosításához kijelölt beállítások](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Jelölje be az **Elfogadom az SQL Server szolgáltatás újraindítását a virtuális gépen** jelölőnégyzetet, majd válassza a **Megerősítés** lehetőséget az IaaS mód teljes re frissítéséhez. 

    ![Jelölőnégyzet az SQL Server szolgáltatás virtuális gépen történő újraindításához](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Parancssor

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Futtassa az Az CLI kódrészletet:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Futtassa a következő PowerShell-kódrészletet:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Regisztráció állapotának ellenőrzése
Ellenőrizheti, hogy az SQL Server virtuális gép már regisztrálva van-e az SQL virtuálisgép-erőforrás-szolgáltatóval az Azure Portalon, az Azure CLI-n vagy a PowerShellen keresztül. 

### <a name="azure-portal"></a>Azure portál 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
1. Nyissa meg az [SQL Server virtuális gépeit](virtual-machines-windows-sql-manage-portal.md).
1. Válassza ki az SQL Server virtuális gép a listából. Ha az SQL Server virtuális gép nem szerepel itt, valószínűleg nem regisztrált az SQL vm erőforrás-szolgáltató. 
1. Az érték megtekintése az **Állapot**csoportban. Ha az **állapot** **sikeres,** akkor az SQL Server virtuális gép sikeresen regisztrálva lett az SQL vm erőforrás-szolgáltatóval. 

![Állapot ellenőrzése SQL RP-regisztrációval](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Parancssor

Ellenőrizze az SQL Server virtuális gép aktuális regisztrációs állapotát az Az CLI vagy a PowerShell használatával. `ProvisioningState`megmutatja, `Succeeded` hogy a regisztráció sikeres volt-e. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Egy hiba azt jelzi, hogy az SQL Server virtuális gép nincs regisztrálva az erőforrás-szolgáltatónál. 


## <a name="unregister-vm-from-rp"></a>Virtuális gép regisztrációjának megszüntetése RP-ből

Az SQL Server virtuális gép regisztrációjának törléséhez az SQL VM-erőforrásszolgáltatóval törölje az SQL *Virtuálisgép-erőforrást* az Azure Portalon vagy az Azure CLI-n keresztül. Az SQL Virtuálisgép *erőforrás* törlése nem törli az SQL Server virtuális gépet. Ugyanakkor legyen óvatos, és gondosan kövesse a lépéseket, mert az *erőforrás*eltávolításakor véletlenül törölheti a virtuális gépet. 

Az SQL virtuális gép törlésére az SQL virtuális gép erőforrás-szolgáltatószükséges a felügyeleti mód teljes leminősítéséhez. 

### <a name="azure-portal"></a>Azure portál

Ha törölni szeretné az SQL Server virtuális gép regisztrációját az erőforrás-szolgáltatóval az Azure Portal használatával, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az SQL Server virtuálisgép-erőforrást. 
  
   ![SQL virtuális gépek erőforrás](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Válassza a **Törlés** elemet. 

   ![SQL vm erőforrás-szolgáltató törlése](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Írja be az SQL virtuális gép nevét, és **törölje a jelet a virtuális gép melletti jelölőnégyzetből.**

   ![SQL vm erőforrás-szolgáltató törlése](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Ha nem törli a jelet a virtuális gép neve melletti jelölőnégyzetből, akkor a virtuális gép teljesen *törlődik.* Törölje a jelet a jelölőnégyzetből az SQL Server virtuális gép erőforrás-szolgáltatótól való regisztrációjának törléséhez, de *a tényleges virtuális gép törléséhez.* 

1. A **Törlés gombra** a virtuális gép *SQL-erőforrása*törlésének megerősítéséhez, és nem az SQL Server virtuális gép törlésének megerősítéséhez. 

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Ha törölni szeretné az SQL Server virtuális gép regisztrációját az erőforrás-szolgáltatótól az Azure CLI-vel, használja az [sql vm delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) parancsot. Ezzel eltávolítja az SQL Server *virtuálisgép-erőforrást,* de nem törli a virtuális gépet. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Ha törölni szeretné az SQL Server virtuális gép regisztrációját az erőforrás-szolgáltatótól az Azure CLI-vel, használja a [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)parancsot. Ezzel eltávolítja az SQL Server *virtuálisgép-erőforrást,* de nem törli a virtuális gépet. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Korlátozások

Az SQL VM erőforrás-szolgáltató csak a következőket támogatja:
- Az Azure Resource Manageren keresztül telepített SQL Server virtuális gépek. A klasszikus modellen keresztül telepített SQL Server virtuális gépek nem támogatottak. 
- Sql Server virtuális gépek a nyilvános vagy az Azure Government felhőben telepített. Más magán- vagy kormányzati felhőkbe történő központi telepítések nem támogatottak. 


## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**Regisztráljam az SQL Server virtuális gépemet egy SQL Server-lemezképből az Azure Marketplace-en?**

Nem. A Microsoft automatikusan regisztrálja az SQL Server-rendszerképekről az Azure Marketplace-en kiépített virtuális gépeket. Az SQL VM erőforrás-szolgáltatóval való regisztráció csak akkor szükséges, ha a virtuális gép *nem* lett kiépítve az SQL Server rendszerképekből az Azure Marketplace-en, és az SQL Server saját telepítéssel volt telepítve.

**Az SQL VM-erőforrásszolgáltató minden ügyfél számára elérhető?** 

Igen. Az ügyfeleknek regisztrálniuk kell az SQL Server virtuálisgép-szolgáltatással az SQL virtuálisgép-erőforrás-szolgáltatónál, ha nem az Azure Marketplace-ről, hanem saját telepítésű SQL Server rendszerképről nem használták az SQL Server rendszerképét, vagy ha egyéni virtuális merevlemezt hoztak. Az összes típusú előfizetés (Közvetlen, Nagyvállalati szerződés és felhőalapú megoldásszolgáltató) tulajdonában lévő virtuális gépek regisztrálhatnak az SQL virtuálisgép-erőforrás-szolgáltatónál.

**Regisztráljak az SQL VM erőforrás-szolgáltatónál, ha az SQL Server virtuális gépemmár telepítette az SQL Server IaaS-bővítményt?**

Ha az SQL Server virtuális gép saját telepített, és nem az SQL Server rendszerképek az Azure Marketplace-en, regisztrálnia kell az SQL VM erőforrás-szolgáltató akkor is, ha telepítette az SQL Server IaaS-bővítményt. Az SQL VM erőforrás-szolgáltatóval történő regisztráció egy Microsoft.SqlVirtualMachines típusú új erőforrást hoz létre. Az SQL Server IaaS-bővítmény telepítése nem hozza létre az erőforrást.

**Mi az alapértelmezett felügyeleti mód az SQL virtuálisgép-erőforrás-szolgáltatóval való regisztrációkor?**

Az alapértelmezett felügyeleti mód, amikor regisztrál az SQL VM erőforrás-szolgáltatóval, *megtelt.* Ha az SQL Server felügyeleti tulajdonság nincs beállítva, amikor regisztrál az SQL Virtuálisgép-erőforrás-szolgáltatóval, a mód teljes körű kezelhetőségként lesz beállítva, és az SQL Server szolgáltatás újraindul. Javasoljuk, hogy regisztráljon az SQL virtuálisgép erőforrás-szolgáltató könnyű üzemmódban először, majd frissítsen teljes karbantartás i a karbantartási időszak alatt. 

**Melyek az SQL virtuálisgép-erőforrás-szolgáltatóval való regisztráció előfeltételei?**

Nincsenek előfeltételei az SQL virtuálisgép-erőforrás-szolgáltatóval való regisztrációnak könnyű vagy ügynök nélküli módban. Az SQL VM erőforrás-szolgáltatóval való teljes körű regisztráció előfeltétele, hogy az SQL Server IaaS bővítmény telepítve legyen a virtuális gépre, ellenkező esetben az SQL Server szolgáltatás újraindul. 

**Regisztrálhatok az SQL VM erőforrás-szolgáltatónál, ha nincs telepítve az SQL Server IaaS-bővítmény a virtuális gépre?**

Igen, regisztrálhat az SQL VM erőforrás-szolgáltató könnyű felügyeleti módban, ha nincs telepítve az SQL Server IaaS-bővítmény telepítve a virtuális gép. Könnyű módban az SQL VM erőforrás-szolgáltató egy konzolalkalmazást fog használni az SQL Server-példány verziójának és kiadásának ellenőrzéséhez. 

Az alapértelmezett SQL-kezelési mód az SQL VM erőforrás-szolgáltatóval való regisztrációkor _a Full_. Ha az SQL Management tulajdonság nincs beállítva az SQL VM erőforrás-szolgáltatóval való regisztrációkor, akkor a mód teljes kezelhetőségként lesz beállítva. Javasoljuk, hogy regisztráljon az SQL virtuálisgép erőforrás-szolgáltató könnyű üzemmódban először, majd frissítsen teljes karbantartás i a karbantartási időszak alatt. 

**Az SQL Virtuálisgép-erőforrás-szolgáltatóval való regisztráció egy ügynököt telepít a virtuális gépre?**

Nem. Az SQL virtuálisgép-erőforrás-szolgáltatóval való regisztráció csak egy új metaadat-erőforrást hoz létre. Nem telepít i.

Az SQL Server IaaS-bővítmény csak a teljes kezelhetőség engedélyezéséhez szükséges. A kezelhetőségi mód könnyűről teljesre történő frissítése telepíti az SQL Server IaaS bővítményt, és újraindítja az SQL Server t.

**Az SQL Server virtuálisgép-erőforrás-szolgáltatóval való regisztráció újraindítja az SQL Servert a virtuális gépen?**

Ez a regisztráció során megadott módtól függ. Ha könnyű vagy NoAgent mód van megadva, akkor az SQL Server szolgáltatás nem indul újra. Ha azonban a felügyeleti módot teljesként adja meg, vagy ha üresen hagyja a felügyeleti módot, az SQL IaaS bővítmény teljes felügyeleti módban lesz, ami az SQL Server szolgáltatás újraindítását eredményezi. 

**Mi a különbség a könnyű és a nem ügynök felügyeleti módok az SQL Virtuálisgép-erőforrás-szolgáltatóval való regisztráció során?** 

Az ügynökkezelési mód csak windows Server 2008 rendszeren érhető el az SQL Server 2008 és az SQL Server 2008 R2 rendszerben. Ez az egyetlen elérhető felügyeleti mód ezekhez a verziókhoz. Az SQL Server összes többi verziója esetében a két rendelkezésre álló kezelhetőségi mód könnyű és teljes. 

Az ügynök nélküli módnak az SQL Server verziójának és kiadásainak beállítását az ügyfélnek kell beállítania. A könnyű mód lekérdezi a virtuális gépszámára az SQL Server-példány verzióját és kiadását.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatónál az SQL Server licenctípusának megadása nélkül?**

Nem. Az SQL Server licenctípusa nem választható tulajdonság, amikor az SQL VM erőforrás-szolgáltatóval regisztrál. Az SQL Server licenctípusát csak ennyire használja, vagy az Azure Hybrid Benefit szolgáltatást, amikor az SQL Virtuálisgép-erőforrás-szolgáltatóval regisztrál minden felügyeleti módban (ügynök nélküli, könnyű és teljes).

**Frissíthetem az SQL Server IaaS-bővítményt ügynök nélküli módból teljes módba?**

Nem. A kezelhetőségi mód teljes vagy könnyű re történő frissítése nem érhető el ügynök nélküli módban. Ez a Windows Server 2008 technikai korlátozása. Először frissítenie kell az operációs rendszert Windows Server 2008 R2 vagy nagyobb rendszerre, majd teljes felügyeleti módra kell frissítenie. 

**Frissíthetem az SQL Server IaaS bővítményt könnyű módról teljes módra?**

Igen. A kezelhetőségi mód könnyűről teljesre való frissítése a PowerShell vagy az Azure Portal on keresztül támogatott. Újra kell indítani az SQL Server szolgáltatást.

**Visszaminősíthetem az SQL Server IaaS bővítményt teljes módból ügynök nélküli vagy könnyű felügyeleti módra?**

Nem. Az SQL Server IaaS bővítmény felügyeleti módjának visszaminősítése nem támogatott. A kezelhetőségi mód nem minősíthető teljes módból könnyű vagy ügynök nélküli módba, és nem minősíthető vissza könnyű módról ügynök nélküli módra. 

A felügyeleti mód teljes kezelhetőségről való módosításához törölje az SQL Server virtuális gép [regisztrációját](#unregister-vm-from-rp) az SQL Server erőforrás-szolgáltatótól az SQL Server *erőforrás* eldobásával, és regisztrálja újra az SQL Server virtuális gépet az SQL VM erőforrás-szolgáltatóval egy másik felügyeleti módban.

**Regisztrálhatok az SQL VM-erőforrás-szolgáltatóval az Azure Portalról?**

Nem. Az SQL virtuálisgép-erőforrás-szolgáltatóval történő regisztráció nem érhető el az Azure Portalon. Az SQL vm erőforrás-szolgáltatóval történő regisztráció csak az Azure CLI vagy a PowerShell által támogatott. 

**Regisztrálhatok virtuális gépet az SQL VM erőforrás-szolgáltatónál az SQL Server telepítése előtt?**

Nem. A virtuális gépnek legalább egy SQL Server (Database Engine) példánysal kell rendelkeznie az SQL VM erőforrás-szolgáltatóval való sikeres regisztrációhoz. Ha nincs SQL Server-példány a virtuális gépen, az új Microsoft.SqlVirtualMachine erőforrás sikertelen állapotban lesz.

**Regisztrálhatok egy virtuális gépet az SQL virtuálisgép-erőforrás-szolgáltatónál, ha több SQL Server-példány van?**

Igen. Az SQL VM erőforrás-szolgáltató csak egy SQL Server (Database Engine) példányt regisztrál. Az SQL VM erőforrás-szolgáltató regisztrálja az alapértelmezett SQL Server-példányt több példány esetén. Ha nincs alapértelmezett példány, akkor csak a könnyű módban történő regisztráció támogatott. A könnyű ről a teljes felügyeleti módra való frissítéshez vagy az alapértelmezett SQL Server-példánynak léteznie kell, vagy a virtuális gépnek csak egy nevű SQL Server-példánysal kell rendelkeznie.

**Regisztrálhatok egy SQL Server feladatátvevő fürtpéldányt az SQL VM erőforrás-szolgáltatóval?**

Igen. Az SQL Server feladatátvételi fürtpéldányai egy Azure virtuális gépen regisztrálhatók az SQL VM erőforrás-szolgáltatóval könnyű módban. Az SQL Server feladatátvevő fürtpéldányai azonban nem frissíthetők teljes felügyeleti módra.

**Regisztrálhatom a virtuális gépemet az SQL virtuálisgép-erőforrás-szolgáltatónál, ha egy Mindig elérhető állási csoport van konfigurálva?**

Igen. Nincsenek korlátozások egy SQL Server-példány regisztrálására egy Azure-beli virtuális gépen az SQL virtuálisgép-erőforrás-szolgáltatóval, ha mindig rendelkezésre állási csoport konfigurációjában vesz részt.

**Mi a költsége az SQL virtuális gép erőforrás-szolgáltató, vagy a teljes körű kezelhetőségi módra való frissítés?**
Nincs. Nincs díj az SQL virtuálisgép-erőforrás-szolgáltatónál történő regisztrációért, vagy a három kezelhetőségi mód bármelyikének használatáért. Az SQL Server virtuális gép kezelése az erőforrás-szolgáltatóval teljesen ingyenes. 

**Milyen hatással van a különböző kezelhetőségi módok teljesítményre?**
Nincs hatással a *NoAgent* és a *könnyű* kezelhetőségi módok használatakor. Minimális hatással van, ha a *teljes* kezelhetőségi módot használja két, az operációs rendszerbe telepített szolgáltatásból. Ezek a feladatkezelőn keresztül figyelhetők, és a beépített Windows Services konzolon láthatók. 

A két szolgáltatásnév a következő:
- `SqlIaaSExtensionQuery`(Megjelenítendő `Microsoft SQL Server IaaS Query Service`név - )
- `SQLIaaSExtension`(Megjelenítendő `Microsoft SQL Server IaaS Agent`név - )


## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server rendszerrel kapcsolatos kérdésekről Windows VM rendszeren](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server díjszabási útmutatója Windows virtuális gépen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Kibocsátási megjegyzések az SQL Server rendszerhez Windows vM rendszeren](virtual-machines-windows-sql-server-iaas-release-notes.md)
