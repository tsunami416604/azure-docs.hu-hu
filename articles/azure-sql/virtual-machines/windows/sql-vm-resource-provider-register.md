---
title: Regisztrálás az SQL VM erőforrás-szolgáltatóval
description: Regisztrálja Azure SQL Server virtuális gépét az SQL VM erőforrás-szolgáltatóval, hogy lehetővé tegye az Azure Marketplace-en kívül üzembe helyezett SQL Server virtuális gépek funkcióinak, valamint a megfelelőség és a jobb kezelhetőség biztosítását.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f1a9a2756d81765d82938651672e5a83edc48ed
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078686"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>SQL Server VM regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval (RP)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan regisztrálhatók a SQL Server virtuális gépek (VM-EK) az Azure-ban az SQL VM erőforrás-szolgáltatóval (RP). Az erőforrás-szolgáltatóval való regisztráció az előfizetésen belül hozza létre az SQL-alapú **virtuális gép** _erőforrását_ , amely a virtuális gép erőforrásának külön erőforrása. A SQL Server VM erőforrás-szolgáltatóból való regisztrációjának törlése eltávolítja az **SQL-alapú virtuális gép** _erőforrását_ , de nem fogja eldobni a tényleges virtuális gépet. 

SQL Server VM Azure Marketplace-rendszerkép üzembe helyezése a Azure Portal használatával automatikusan regisztrálja az SQL Server VM az erőforrás-szolgáltatóval. Ha azonban úgy dönt, hogy egy Azure-beli virtuális gépen telepíti a SQL Servert, vagy egyéni virtuális MEREVLEMEZről szeretne üzembe helyezni egy Azure-beli virtuális gépet, akkor a következő erőforrás-szolgáltatóval kell regisztrálnia a SQL Server VM:

- A **szolgáltatás előnyei**: a SQL Server VM erőforrás-szolgáltatóval való regisztrálása feloldja az [automatizált javításokat](automated-patching.md), az [automatikus biztonsági mentést](automated-backup.md), valamint a figyelési és kezelhetőségi képességeket. Emellett feloldja a [licencelési](licensing-model-azure-hybrid-benefit-ahb-change.md) és a [kiadási](change-sql-server-edition.md) rugalmasságot is. Ezek a funkciók korábban csak az Azure Marketplace-en üzembe helyezett lemezképek SQL Server VM voltak elérhetők. 

- **Megfelelőség**: az SQL VM erőforrás-szolgáltatóval való regisztráció egyszerűsített módszert kínál a Microsoft értesítésére, hogy a Azure Hybrid Benefit a termék feltételeiben megadott módon van engedélyezve. Ez a folyamat cáfolja az egyes erőforrások licencelési regisztrációs űrlapjainak kezelését.  

- **Ingyenes felügyelet**: az SQL VM erőforrás-szolgáltatóval való regisztráció mindhárom kezelhetőségi módban teljesen ingyenes. Az erőforrás-szolgáltatóval kapcsolatban nem áll rendelkezésre további díj, vagy a felügyeleti módokat módosítani kell. 

- **Egyszerűsített licenckezelő**: az SQL VM erőforrás-szolgáltatóval való regisztrálás leegyszerűsíti SQL Server a licencek kezelését, és lehetővé teszi SQL Server virtuális gépek gyors azonosítását az [Azure Portal](manage-sql-vm-portal.md), az Azure CLI vagy a PowerShell használatával Azure Hybrid Benefit engedélyezve. 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Az SQL VM erőforrás-szolgáltató használatához először [regisztrálnia kell az előfizetést az erőforrás-szolgáltatóval](#register-subscription-with-rp), amely lehetővé teszi az erőforrás-szolgáltató számára, hogy erőforrásokat hozzon létre az adott előfizetésen belül.

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM erőforrás-szolgáltatóval való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- A nyilvános vagy Azure Government felhőben üzembe helyezett Azure-beli erőforrás-modell [SQL Server VM](create-sql-vm-portal.md) . 
- Az [Azure CLI](/cli/azure/install-azure-cli) vagy a [PowerShell](/powershell/azure/new-azureps-module-az)legújabb verziója. 

## <a name="management-modes"></a>Felügyeleti módok

Ha az [SQL IaaS bővítmény](sql-server-iaas-agent-extension-automate-management.md) még nincs telepítve, a regisztrálás az SQL VM erőforrás-szolgáltatónál automatikusan telepíti a SQL Server IaaS-bővítményt a regisztrációs folyamat során megadott három felügyeleti mód egyikében. A felügyeleti mód nem határozza meg, hogy az SQL IaaS-bővítményt teljes felügyeleti módban telepíti.  

Ha az SQL IaaS-bővítményt manuálisan telepítette, akkor már teljes felügyeleti módban van, és az erőforrás-szolgáltatóhoz való regisztráció teljes módban nem fogja újraindítani a SQL Server szolgáltatást.

A három felügyeleti mód a következők:

- A **könnyű** mód nem igényli a SQL Server újraindítását, de csak az SQL Server licenc típusának és kiadásának módosítását támogatja. Ezzel a beállítással SQL Server virtuális gépeket több példánnyal, vagy részt vehet a feladatátvevő fürt példányaiban. A könnyű mód használata esetén nincs hatással a memóriára vagy a CPU-ra, és nincs kapcsolódó díj. Javasoljuk, hogy először regisztrálja a SQL Server VMt, majd egy ütemezett karbantartási időszak alatt frissítsen a teljes módba.  

- A **teljes** mód biztosítja az összes funkciót, de a SQL Server és a rendszergazdai engedélyek újraindítását igényli. Ez az a beállítás, amely alapértelmezés szerint telepítve van az SQL IaaS bővítmény manuális telepítésekor. A SQL Server VM egyetlen példánnyal való felügyeletére használható. A teljes mód két Windows-szolgáltatást telepít, amelyek minimális hatással vannak a memóriára és a CPU-ra – ezeket a Feladatkezelő segítségével figyelheti. A teljes kezelhetőségi mód használata nem jár együtt. 

- A nem **ügynök** mód a Windows Server 2008 rendszeren telepített SQL Server 2008 és SQL Server 2008 R2 rendszerre van kijelölve. A nem befolyásolja a memóriát vagy a CPU-t a nem ügynök mód használata esetén. A nem ügynöki kezelhetőségi mód használata nem jár együtt. 

A SQL Server IaaS-ügynök aktuális módja a PowerShell használatával tekinthető meg: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Előfizetés regisztrálása az RP-vel

A SQL Server VM az SQL VM erőforrás-szolgáltatóval való regisztrálásához először regisztrálnia kell az előfizetést az erőforrás-szolgáltatóval. Ezzel az SQL VM erőforrás-szolgáltató lehetővé teszi, hogy erőforrásokat hozzon létre az előfizetésében.  Ezt a Azure Portal, az Azure CLI vagy a PowerShell használatával teheti meg.

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a Azure Portalt, és válassza a **minden szolgáltatás**lehetőséget. 
1. Lépjen az **előfizetések** elemre, és válassza ki a kamat előfizetését.  
1. Az **előfizetések** lapon lépjen az **erőforrás-szolgáltatók**elemre. 
1. Adja **meg az SQL-** t a szűrőben az SQL-hez kapcsolódó erőforrás-szolgáltatók létrehozásához. 
1. Válassza a **Microsoft. SqlVirtualMachine** -szolgáltató **regisztrálása**, **újbóli regisztrálása**vagy **regisztrációjának** törlése lehetőséget a kívánt művelettől függően. 

   ![A szolgáltató módosítása](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Parancssor

Regisztrálja az SQL-alapú virtuális gép erőforrás-szolgáltatóját az Azure-előfizetésben az Azure CLI vagy a PowerShell használatával. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Regisztrálás az RP-mel

### <a name="lightweight-management-mode"></a>Könnyűsúlyú felügyeleti mód

Ha a [SQL Server IaaS-ügynök bővítmény](sql-server-iaas-agent-extension-automate-management.md) nincs telepítve a virtuális gépen, akkor a javaslat az SQL VM erőforrás-szolgáltató egyszerűsített módban való regisztrálására szolgál. Ez a művelet [egyszerűsített módban](#management-modes) telepíti az SQL IaaS bővítményt, és megakadályozza a SQL Server szolgáltatás újraindítását. Ezt követően bármikor frissítheti a teljes üzemmódra, de ez a művelet újraindítja a SQL Server szolgáltatást, ezért ajánlott megvárni az ütemezett karbantartási időszakot. 

Az ingyenes DR replika-licenc aktiválásához adja meg az SQL Server-licenc típusát (), ha használati díjat szeretne fizetni `PAYG` , Azure Hybrid Benefit ( `AHUB` ) használja a saját licencét, vagy a vész-helyreállítást ( `DR` ). [free DR replica license](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)

A feladatátvevő fürtök példányai és a többpéldányos központi telepítések csak az SQL VM erőforrás-szolgáltatón keresztül regisztrálhatók egyszerűsített módban. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

SQL Server VM regisztrálása könnyűsúlyú módban az Azure CLI-vel: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

SQL Server VM regisztrálása könnyűsúlyú módban a PowerShell használatával:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Teljes felügyeleti mód


Ha az SQL IaaS bővítményt manuálisan telepítette a virtuális gépre, akkor a SQL Server szolgáltatás újraindítása nélkül is regisztrálhatja a SQL Server VM teljes módban. **Ha azonban az SQL IaaS bővítmény nincs telepítve, a teljes módú regisztráció során az SQL IaaS-bővítményt teljes módban telepíti, majd újraindítja a SQL Server szolgáltatást. Kérjük, körültekintően járjon el.**


Ha a SQL Server VMt közvetlenül teljes módban szeretné regisztrálni (és valószínűleg újraindítani a SQL Server szolgáltatást), használja a következő PowerShell-parancsot: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Ügynök-felügyeleti mód 

A Windows Server 2008 (_nem R2_) rendszerre telepített 2008-es és 2008 R2-es SQL Server a nem [ügynök módban](#management-modes)regisztrálható az SQL VM erőforrás-szolgáltatónál. Ez a beállítás biztosítja a megfelelőséget, és lehetővé teszi a SQL Server VM figyelését a Azure Portal korlátozott funkcionalitással.

Adjon meg `AHUB` vagy `PAYG` `DR` **sqlLicenseType**, vagy `SQL2008-WS2008` `SQL2008R2-WS2008` **sqlImageOffer**. 

A SQL Server 2008-as vagy 2008 R2-es verziónak a Windows Server 2008-példányon való regisztrálásához használja a következő Azure CLI-vagy PowerShell-kódrészletet: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Regisztrálja a SQL Server 2008-es virtuális gépet a (z) Azure CLI-ben a nem ügynök módban: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Regisztrálja SQL Server 2008 R2 rendszerű virtuális gépet az Azure CLI-vel együtt: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Regisztrálja SQL Server 2008 virtuális gépet a PowerShell használatával: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Regisztrálja SQL Server 2008 R2 rendszerű virtuális gépet a PowerShell használatával: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>Frissítés teljesre  

SQL Server a *Lightweight* IaaS bővítménnyel telepített virtuális gépek a Azure Portal, az Azure CLI vagy a PowerShell használatával _teljes mértékben_ frissíthetik a módot. SQL Server a nem _ügynök_ módban lévő virtuális gépek az operációs rendszer Windows 2008 R2 vagy újabb verzióra való frissítése után _teljes egészében_ frissíthetnek. Nem lehetséges a visszalépés – ehhez meg kell [szüntetnie a SQL Server VM regisztrációját](#unregister-from-rp) az SQL VM erőforrás-szolgáltatójából. Ezzel a művelettel eltávolítja az SQL-alapú **virtuális gép** _erőforrását_, de nem törli a tényleges virtuális gépet. 

A SQL Server IaaS-ügynök aktuális módja a PowerShell használatával tekinthető meg: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Az ügynök üzemmódjának teljes frissítése: 


### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az SQL-alapú [virtuális gépek](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) erőforrását. 
1. Válassza ki a SQL Server VM, és válassza az **Áttekintés**lehetőséget. 
1. A nem ügynökkel vagy Lightweight IaaS-móddal rendelkező SQL Server virtuális gépek esetében válassza az **SQL IaaS kiterjesztési üzenetében csak a licenc típusát és a kiadási frissítéseket** .

   ![A mód a portálról való módosításának kiválasztása](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Jelölje be az **Elfogadom a SQL Server szolgáltatás újraindítása a virtuális gépen** jelölőnégyzetet, majd válassza a **megerősítés** lehetőséget a IaaS mód teljes állapotra való frissítéséhez. 

    ![A virtuális gépen SQL Server szolgáltatás újraindítását kérő jelölőnégyzet](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Futtassa az alábbi Azure CLI-kódrészletet:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Futtassa a következő PowerShell-kódrészletet:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Regisztrációs állapot ellenőrzése
A Azure Portal, az Azure CLI vagy a PowerShell használatával ellenőrizheti, hogy a SQL Server VM már regisztrálva van-e az SQL VM erőforrás-szolgáltatóban. 

### <a name="azure-portal"></a>Azure Portal 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Nyissa meg a [SQL Server virtuális gépeket](manage-sql-vm-portal.md).
1. Válassza ki a SQL Server VM a listából. Ha a SQL Server VM nem szerepel a listán, valószínűleg nincs regisztrálva az SQL VM erőforrás-szolgáltatónál. 
1. Tekintse meg az **állapot**alatt lévő értéket. Ha **Status** az állapot **sikeres**, akkor a SQL Server VM sikeresen regisztrálva lett az SQL VM erőforrás-szolgáltatónál. 

   ![SQL RP-regisztrációval rendelkező állapot ellenőrzése](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Parancssor

Ellenőrizze az aktuális SQL Server VM regisztrációs állapotot az Azure CLI vagy a PowerShell használatával. `ProvisioningState` azt mutatja, `Succeeded` hogy a regisztráció sikeres volt-e. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

A hiba azt jelzi, hogy a SQL Server VM nincs regisztrálva az erőforrás-szolgáltatónál. 


## <a name="unregister-from-rp"></a>Regisztráció törlése az RP-ből

Ha meg szeretné szüntetni a SQL Server VM regisztrációját az SQL VM erőforrás-szolgáltatóval, törölje az SQL virtuális gép *erőforrását* az Azure Portal vagy az Azure CLI használatával. Az SQL-alapú virtuális gép *erőforrásának* törlése nem törli a SQL Server VM. Azonban körültekintően járjon el, és gondosan kövesse a lépéseket, mert lehetséges, hogy véletlenül törli a virtuális gépet az *erőforrás*eltávolítására tett kísérlet során. 

Az SQL virtuális gép erőforrás-szolgáltatóval való regisztrációjának törlése szükséges a felügyeleti mód teljes körű visszalépéséhez. 

### <a name="azure-portal"></a>Azure Portal

A SQL Server VM az erőforrás-szolgáltatóval való regisztrációjának megszüntetéséhez a Azure Portal használatával hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon az SQL VM-erőforráshoz. 
  
   ![SQL-alapú virtuális gépek erőforrása](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Válassza a **Törlés** elemet. 

   ![SQL-alapú virtuális gép erőforrás-szolgáltatójának törlése](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Írja be az SQL-virtuális gép nevét, és **törölje a virtuális gép melletti jelölőnégyzet**jelölését.

   ![SQL-alapú virtuális gép erőforrás-szolgáltatójának törlése](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nem sikerült törölni a virtuális gép neve melletti jelölőnégyzetet a virtuális gép teljes *törléséhez* . Törölje a jelet a jelölőnégyzetből a SQL Server VM regisztrációjának törléséhez az erőforrás-szolgáltatónál, de *ne törölje a tényleges virtuális gépet*. 

1. Válassza a **Törlés** lehetőséget az SQL-alapú virtuális gép *erőforrásának*törlésének megerősítéséhez, és ne a SQL Server VM. 

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Ha meg szeretné szüntetni a SQL Server VM regisztrációját az erőforrás-szolgáltatóból az Azure CLI-vel, használja az az [SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást* , de nem törli a virtuális gépet. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ha törölni szeretné a SQL Server VM regisztrációját az erőforrás-szolgáltatóból az Azure CLI-vel, használja a [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást* , de nem törli a virtuális gépet. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Korlátozások

Az SQL VM erőforrás-szolgáltató csak a következőket támogatja:
- SQL Server a Azure Resource Manager üzembe helyezett virtuális gépeket. SQL Server a klasszikus modellen keresztül üzembe helyezett virtuális gépek nem támogatottak. 
- SQL Server a nyilvános vagy Azure Government felhőbe telepített virtuális gépeket. A többi magán-vagy kormányzati felhőbe való üzembe helyezés nem támogatott. 


## <a name="frequently-asked-questions"></a>Gyakori kérdések 

**Regisztrálhatom a SQL Server VM az Azure Marketplace-en egy SQL Server rendszerképből kiépítve?**

Nem. A Microsoft automatikusan regisztrálja az Azure Marketplace-en SQL Server rendszerképből kiépített virtuális gépeket. Az SQL VM erőforrás-szolgáltatóval való regisztrálás csak akkor szükséges, ha a virtuális gép *nem* lett kiépítve az Azure Marketplace SQL Server rendszerképeiből, és SQL Server önálló telepítése történt.

**Az SQL VM-erőforrásszolgáltató minden ügyfél számára elérhető?** 

Igen. Az ügyfeleknek regisztrálniuk kell a SQL Server virtuális gépeket az SQL VM erőforrás-szolgáltatóval, ha nem használnak SQL Server rendszerképet az Azure Marketplace-ről, hanem önállóan telepített SQL Server, vagy ha az egyéni VHD-t hozták. Az összes típusú előfizetés (Direct, Nagyvállalati Szerződés és Cloud Solution Provider) tulajdonában lévő virtuális gépek regisztrálhatnak az SQL VM erőforrás-szolgáltatóval.

**Regisztrálni kell az SQL VM erőforrás-szolgáltatónál, ha a SQL Server VM már telepítve van a SQL Server IaaS bővítmény?**

Ha a SQL Server VM öntelepített, és nem az Azure Marketplace-en lévő SQL Server lemezképből lett kiépítve, akkor is regisztrálnia kell az SQL VM erőforrás-szolgáltatóval, ha telepítette a SQL Server IaaS bővítményt. Az SQL virtuális gép erőforrás-szolgáltatójának regisztrálása egy új, Microsoft. SqlVirtualMachine típusú erőforrást hoz létre. A SQL Server IaaS bővítmény telepítése nem hozza létre az erőforrást.

**Mi az alapértelmezett felügyeleti mód az SQL VM erőforrás-szolgáltatóval való regisztráláskor?**

Az SQL VM erőforrás-szolgáltatóval való regisztráláskor az alapértelmezett felügyeleti mód *megtelt*. Ha a SQL Server Management tulajdonság nincs beállítva az SQL VM erőforrás-szolgáltatóhoz való regisztráláskor, a mód teljes kezelhetőségként lesz beállítva, és a SQL Server szolgáltatás újra fog indulni. Javasoljuk, hogy először az egyszerűsített módban regisztrálja az SQL VM erőforrás-szolgáltatót, majd a karbantartási időszak alatt teljesre frissítsen. 

**Milyen előfeltételeket kell regisztrálni az SQL VM erőforrás-szolgáltatónál?**

Az SQL VM erőforrás-szolgáltatóhoz nem szükséges előfeltétel, hogy egyszerűsített módban vagy nem ügynök módban regisztráljanak. Az SQL VM erőforrás-szolgáltató teljes módban való regisztrálásának előfeltétele, hogy a SQL Server IaaS bővítmény telepítve legyen a virtuális gépen, ellenkező esetben a SQL Server szolgáltatás újra fog indulni. 

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval, ha nincs telepítve a SQL Server IaaS bővítmény a virtuális gépre?**

Igen, ha nem rendelkezik a virtuális gépre telepített SQL Server IaaS bővítménnyel, akkor az egyszerűsített felügyeleti módban regisztrálhatja az SQL VM erőforrás-szolgáltatót. A könnyű mód esetében az SQL VM erőforrás-szolgáltató egy konzol alkalmazást fogja használni a SQL Server példány verziójának és kiadásának ellenőrzéséhez. 

Az SQL VM erőforrás-szolgáltatóval való regisztráláskor az alapértelmezett SQL-kezelési mód _megtelt_. Ha az SQL-alapú virtuális gép erőforrás-szolgáltatójának regisztrálásakor nincs beállítva az SQL Management tulajdonság, a mód teljes Kezelhetőségként lesz beállítva. Javasoljuk, hogy először az egyszerűsített módban regisztrálja az SQL VM erőforrás-szolgáltatót, majd a karbantartási időszak alatt teljesre frissítsen. 

**Regisztrálja az SQL VM erőforrás-szolgáltatót az ügynök telepítése a virtuális gépre?**

Igen, az SQL VM erőforrás-szolgáltatóval való regisztrálás egy ügynököt telepít a virtuális gépre.

A SQL Server IaaS bővítmény az ügynökre támaszkodik a SQL Server metaadatainak lekérdezéséhez. Az ügynök telepítése csak akkor történik meg, ha az SQL-alapú virtuális gép erőforrás-szolgáltatója nem regsitered módban van.

**Regisztrálja az SQL VM erőforrás-szolgáltató újraindítási SQL Server a virtuális gépen?**

Ez a regisztráció során megadott módtól függ. Ha a Lightweight vagy a nem ügynök mód van megadva, akkor a SQL Server szolgáltatás nem indul újra. Azonban a felügyeleti mód teljesként való megadása vagy a felügyeleti mód üresen hagyása esetén az SQL IaaS-bővítményt teljes felügyeleti módban telepíti a rendszer, ami miatt a SQL Server szolgáltatás újraindul. 

**Mi a különbség a könnyű és a nem ügynök felügyeleti módok között az SQL VM erőforrás-szolgáltatóval való regisztráláskor?** 

A nem ügynök felügyeleti mód csak SQL Server 2008 és SQL Server 2008 R2 rendszer esetén érhető el a Windows Server 2008-on. Ezen verziók esetében ez az egyetlen elérhető felügyeleti mód. A SQL Server összes többi verziója esetében a két elérhető kezelhetőségi mód könnyű és teljes. 

A nem ügynök üzemmódhoz az ügyfélnek meg kell adni SQL Server Version és Edition tulajdonságokat. A könnyű mód lekérdezi a virtuális gépet a SQL Server példány verziójának és kiadásának megkereséséhez.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval a SQL Server licenc típusának megadása nélkül?**

Nem. Az SQL Server licenc típusa nem választható tulajdonság az SQL VM erőforrás-szolgáltatóval való regisztráláskor. Ha az SQL VM erőforrás-szolgáltatót az összes kezelhetőségi módban (nem ügynök, Lightweight és teljes) regisztrálja, akkor a SQL Server licenc Azure Hybrid Benefit típusát kell elszámolni.

**Frissíthetem a SQL Server IaaS-bővítményt ügynök nélküli módból teljes módba?**

Nem. A kezelhetőségi mód teljes vagy egyszerű frissítése nem érhető el ügynök módban. Ez a Windows Server 2008 technikai korlátozása. Először a Windows Server 2008 R2 vagy újabb verzióra kell frissítenie az operációs rendszert, majd frissítenie kell a teljes felügyeleti módra. 

**Frissíthetem a SQL Server IaaS-bővítményt az egyszerűsített módból a teljes módba?**

Igen. A kezelhetőségi mód lightweightről teljesre történő frissítése a PowerShell vagy a Azure Portal használatával támogatott. SQL Server szolgáltatás újraindítását igényli.

**Visszaválthatom a SQL Server IaaS-bővítményt a teljes módból a nem ügynök vagy az egyszerűsített felügyeleti módra?**

Nem. A SQL Server IaaS-bővítmény kezelhetőségi módjának lefokozása nem támogatott. A kezelhetőségi mód nem állítható vissza a teljes módból a könnyű vagy nem ügynök módba, és nem lehet leértékelni az egyszerűsített módból az ügynök módba. 

Ha módosítani szeretné a kezelhetőségi módot a teljes kezelhetőséggel, szüntesse meg a SQL Server VM [regisztrációját](#unregister-from-rp) az SQL VM erőforrás-szolgáltatóból a SQL Server *erőforrás* eldobásával, majd újból regisztrálja az SQL Server VMt az SQL VM erőforrás-szolgáltatóval egy másik felügyeleti módban.

**Regisztrálhatok az SQL VM erőforrás-szolgáltatóval a Azure Portal?**

Nem. Az SQL VM erőforrás-szolgáltatóval való regisztráció nem érhető el a Azure Portal. Az SQL VM erőforrás-szolgáltatóval való regisztráció csak az Azure CLI vagy a PowerShell használatával támogatott. 

**Regisztrálhatok egy virtuális gépet az SQL VM erőforrás-szolgáltatóval SQL Server telepítése előtt?**

Nem. A virtuális gépnek legalább egy SQL Server (adatbázismotor) példányával regisztrálnia kell az SQL VM erőforrás-szolgáltatóval. Ha a virtuális gépen nincs SQL Server példány, az új Microsoft. SqlVirtualMachine erőforrás sikertelen állapotba kerül.

**Regisztrálhatok egy virtuális gépet az SQL VM erőforrás-szolgáltatóval, ha több SQL Server példány is van?**

Igen. Az SQL virtuális gép erőforrás-szolgáltatója csak egy SQL Server (adatbázismotor-) példányt fog regisztrálni. Az SQL virtuális gép erőforrás-szolgáltatója több példány esetén regisztrálja az alapértelmezett SQL Server példányt. Ha nincs alapértelmezett példány, akkor a rendszer csak az egyszerűsített módban történő regisztrációt támogatja. A lightweightről teljes kezelhetőségi üzemmódra való frissítéshez vagy az alapértelmezett SQL Server példánynak léteznie kell, vagy a virtuális gépnek csak egy névvel ellátott SQL Server példánynak kell lennie.

**Regisztrálhatok egy SQL Server feladatátvevő fürt példányát az SQL VM erőforrás-szolgáltatóval?**

Igen. Az Azure-beli virtuális gépeken SQL Server feladatátvevő fürt példányai egyszerűsített módban regisztrálhatók az SQL VM erőforrás-szolgáltatóban. SQL Server feladatátvevő fürt példányai azonban nem frissíthetők teljes kezelhetőségi módba.

**Regisztrálhatom a virtuális gépet az SQL VM erőforrás-szolgáltatóval, ha az Always On rendelkezésre állási csoport konfigurálva van?**

Igen. Az SQL VM erőforrás-szolgáltatóval nem lehet SQL Server példányt regisztrálni egy Azure-beli virtuális gépen, ha részt vesz egy always on rendelkezésre állási csoport konfigurációjában.

**Mennyibe kerül az SQL VM erőforrás-szolgáltatóval való regisztráció, vagy a teljes körű kezelhetőségi módra való frissítés?**
Nincsenek. Az SQL VM erőforrás-szolgáltatóval való regisztrációhoz és a három kezelhetőségi mód bármelyikéhez nem tartozik díj. A SQL Server VM erőforrás-szolgáltatóval való kezelése teljesen ingyenes. 

**Milyen hatással van a teljesítmény a különböző kezelhetőségi módok használatával?**
A nem befolyásolható a nem- *ügynök* és a *könnyű* kezelhetőségi mód használata. Az operációs rendszerre telepített két szolgáltatás *teljes* kezelhetőségi módjának használata minimális hatással van. Ezek a Feladatkezelő segítségével figyelhetők meg, és a beépített Windows-szolgáltatások konzolon láthatók. 

A két szolgáltatás neve:
- `SqlIaaSExtensionQuery` (Megjelenítendő név – `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension` (Megjelenítendő név – `Microsoft SQL Server IaaS Agent` )


## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)  
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)
