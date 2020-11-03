---
title: SQL Server IaaS-ügynök bővítményének telepítése
description: Regisztrálja Azure SQL Server virtuális gépét az SQL VM erőforrás-szolgáltatóval, hogy lehetővé tegye az Azure Marketplace-en kívül üzembe helyezett SQL Server virtuális gépek funkcióinak, valamint a megfelelőség és a jobb kezelhetőség biztosítását.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286139"
---
# <a name="install-sql-server-iaas-agent-extension"></a>SQL Server IaaS-ügynök bővítményének telepítése
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A SQL Server VM regisztrálása az SQL VM erőforrás-szolgáltató telepítésekor a [SQL Server IaaS-ügynök bővítményt](sql-server-iaas-agent-extension-automate-management.md)telepíti. 

Ebből a cikkből megtudhatja, hogyan regisztrálhat egyetlen SQL Server VM az SQL VM erőforrás-szolgáltatóval. Azt is megteheti, hogy az összes SQL Server virtuális gépet [automatikusan](sql-vm-resource-provider-automatic-registration.md) vagy [tömegesen](sql-vm-resource-provider-bulk-register.md)is regisztrálja.


## <a name="overview"></a>Áttekintés

Az erőforrás-szolgáltatóval való regisztráció telepíti a [SQL Server IaaS-ügynök bővítményt](sql-server-iaas-agent-extension-automate-management.md) , és az előfizetésen belül létrehozza az SQL-alapú **virtuális gép** _erőforrását_ is, amely a virtuális gép erőforrásának _külön_ erőforrása. A SQL Server VM erőforrás-szolgáltatóból való regisztrációjának törlése eltávolítja az **SQL-alapú virtuális gép** _erőforrását_ , de nem fogja eldobni a tényleges virtuális gépet.

SQL Server VM Azure Marketplace-rendszerkép üzembe helyezése a Azure Portal használatával automatikusan regisztrálja az SQL Server VM az erőforrás-szolgáltatóval. Ha azonban úgy dönt, hogy egy Azure-beli virtuális gépen telepíti a SQL Servert, vagy egyéni virtuális MEREVLEMEZről épít ki egy Azure-beli virtuális gépet, akkor az SQL IaaS-ügynök bővítményének telepítéséhez regisztrálnia kell a SQL Server VM az SQL VM erőforrás-szolgáltatóval. 

Az SQL VM erőforrás-szolgáltató használatához először [regisztrálnia kell az előfizetést az erőforrás-szolgáltatóval](#register-subscription-with-rp), amely lehetővé teszi az erőforrás-szolgáltató számára, hogy erőforrásokat hozzon létre az adott előfizetésen belül.

> [!IMPORTANT]
> Az SQL IaaS-ügynök bővítmény olyan adatokat gyűjt az expressz célra, amelyek az ügyfelek számára opcionális előnyöket biztosítanak SQL Server Azure-beli Virtual Machines való használatakor. A Microsoft ezeket az adatszolgáltatásokat nem használja fel a licencelési naplózáshoz az ügyfél előzetes belefoglalása nélkül. További információért tekintse meg a [SQL Server adatvédelmi kiegészítését](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM erőforrás-szolgáltatóval való regisztrálásához a következőkre lesz szüksége: 

- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy Azure-erőforrás modellje a [Windows Server 2008 (vagy újabb) rendszerű virtuális gép](../../../virtual-machines/windows/quick-create-portal.md) , amely [SQL Server 2008 (vagy újabb)](https://www.microsoft.com/sql-server/sql-server-downloads) rendszert helyez üzembe a nyilvános vagy Azure Government felhőben. 
- Az [Azure CLI](/cli/azure/install-azure-cli) legújabb verziója vagy [Azure PowerShell (minimum 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Előfizetés regisztrálása az RP-vel

A SQL Server VM az SQL VM erőforrás-szolgáltatóval való regisztrálásához először regisztrálnia kell az előfizetést az erőforrás-szolgáltatóval. Ezzel az SQL VM erőforrás-szolgáltató lehetővé teszi, hogy erőforrásokat hozzon létre az előfizetésében.  Ezt a Azure Portal, az Azure CLI vagy a Azure PowerShell használatával teheti meg.

### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a Azure Portalt, és válassza a **minden szolgáltatás** lehetőséget. 
1. Lépjen az **előfizetések** elemre, és válassza ki a kamat előfizetését.  
1. Az **előfizetések** lapon lépjen az **erőforrás-szolgáltatók** elemre. 
1. Adja **meg az SQL-** t a szűrőben az SQL-hez kapcsolódó erőforrás-szolgáltatók létrehozásához. 
1. Válassza a **Microsoft. SqlVirtualMachine** -szolgáltató **regisztrálása** , **újbóli regisztrálása** vagy **regisztrációjának** törlése lehetőséget a kívánt művelettől függően. 

   ![A szolgáltató módosítása](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Parancssor

Regisztrálja az SQL-alapú virtuális gép erőforrás-szolgáltatóját az Azure-előfizetésben az Azure CLI vagy a Azure PowerShell használatával. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Regisztrálás az RP-mel

A [SQL Server IaaS-ügynök bővítményének](sql-server-iaas-agent-extension-automate-management.md#management-modes)három kezelési módja van. 

Ha a bővítményt a teljes felügyeleti módban telepíti, a újraindítja a SQL Server szolgáltatást, ezért azt javasoljuk, hogy először az egyszerűsített módban telepítse a bővítményt, majd a karbantartási időszak alatt [frissítsen a teljes verzióra](#upgrade-to-full) . 

### <a name="lightweight-management-mode"></a>Könnyűsúlyú felügyeleti mód

Az Azure CLI vagy a Azure PowerShell használatával regisztrálja SQL Server VM az erőforrás-szolgáltatónál, és telepítse az SQL IaaS-bővítményt egyszerűsített módban. Ez nem fogja újraindítani a SQL Server szolgáltatást. Ezt követően bármikor frissítheti a teljes üzemmódra, de ez a művelet újraindítja a SQL Server szolgáltatást, ezért ajánlott megvárni az ütemezett karbantartási időszakot. 

Az ingyenes DR replika-licenc aktiválásához adja meg az SQL Server-licenc típusát (), ha használati díjat szeretne fizetni `PAYG` , Azure Hybrid Benefit ( `AHUB` ) használja a saját licencét, vagy a vész-helyreállítást ( `DR` ). [free DR replica license](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)

A feladatátvevő fürtök példányai és a többpéldányos központi telepítések csak az SQL VM erőforrás-szolgáltatón keresztül regisztrálhatók egyszerűsített módban. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

SQL Server VM regisztrálása könnyűsúlyú módban az Azure CLI-vel: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

SQL Server VM regisztrálása egyszerűsített módban Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Teljes felügyeleti mód

Ha a SQL Server VM teljes módban regisztrálja, a rendszer újraindítja a SQL Server szolgáltatást. Kérjük, körültekintően járjon el. 

Ha a SQL Server VMt közvetlenül teljes módban szeretné regisztrálni (és valószínűleg újraindítani a SQL Server szolgáltatást), használja a következő Azure PowerShell parancsot: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Ügynök-felügyeleti mód 

A Windows Server 2008 ( _nem R2_ ) rendszerre telepített 2008-es és 2008 R2-es SQL Server a nem [ügynök módban](sql-server-iaas-agent-extension-automate-management.md#management-modes)regisztrálható az SQL VM erőforrás-szolgáltatónál. Ez a beállítás biztosítja a megfelelőséget, és lehetővé teszi a SQL Server VM figyelését a Azure Portal korlátozott funkcionalitással.

Adjon meg `AHUB` vagy `PAYG` `DR` **sqlLicenseType** , vagy `SQL2008-WS2008` `SQL2008R2-WS2008` **sqlImageOffer**. 

A SQL Server 2008-as vagy 2008 R2-es verziónak a Windows Server 2008-példányon való regisztrálásához használja az alábbi Azure CLI-vagy Azure PowerShell-kódrészletet: 


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

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Regisztrálja SQL Server 2008 virtuális gépet a Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

Regisztrálja SQL Server 2008 R2 rendszerű virtuális gépet a Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>Mód ellenőrzése

A SQL Server IaaS-ügynök aktuális módja a Azure PowerShell használatával tekinthető meg: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Frissítés teljesre  

SQL Server a *Lightweight* IaaS-bővítményt futtató virtuális gépek a Azure Portal, az Azure CLI vagy a Azure PowerShell használatával _teljes mértékben_ frissíthetik a módot. SQL Server a nem _ügynök_ módban lévő virtuális gépek az operációs rendszer Windows 2008 R2 vagy újabb verzióra való frissítése után _teljes egészében_ frissíthetnek. Nem lehetséges a visszalépés – ehhez meg kell [szüntetnie a SQL Server VM regisztrációját](#unregister-from-rp) az SQL VM erőforrás-szolgáltatójából. Ezzel a művelettel eltávolítja az SQL-alapú **virtuális gép** _erőforrását_ , de nem törli a tényleges virtuális gépet. 


### <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Nyissa meg az SQL-alapú [virtuális gépek](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) erőforrását. 
1. Válassza ki a SQL Server VM, és válassza az **Áttekintés** lehetőséget. 
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

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa a következő Azure PowerShell kódrészletet:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Regisztrációs állapot ellenőrzése
A Azure Portal, az Azure CLI vagy a Azure PowerShell használatával ellenőrizheti, hogy a SQL Server VM már regisztrálva van-e az SQL VM erőforrás-szolgáltatóban. 

### <a name="azure-portal"></a>Azure Portal 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Nyissa meg a [SQL Server virtuális gépeket](manage-sql-vm-portal.md).
1. Válassza ki a SQL Server VM a listából. Ha a SQL Server VM nem szerepel a listán, valószínűleg nincs regisztrálva az SQL VM erőforrás-szolgáltatónál. 
1. Tekintse meg az **állapot** alatt lévő értéket. Ha **Status** az állapot **sikeres** , akkor a SQL Server VM sikeresen regisztrálva lett az SQL VM erőforrás-szolgáltatónál. 

   ![SQL RP-regisztrációval rendelkező állapot ellenőrzése](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Parancssor

Ellenőrizze az aktuális SQL Server VM regisztrációs állapotot az Azure CLI vagy a Azure PowerShell használatával. `ProvisioningState` azt mutatja, `Succeeded` hogy a regisztráció sikeres volt-e. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

A hiba azt jelzi, hogy a SQL Server VM nincs regisztrálva az erőforrás-szolgáltatónál. 


## <a name="unregister-from-rp"></a>Regisztráció törlése az RP-ből

Ha meg szeretné szüntetni a SQL Server VM regisztrációját az SQL VM erőforrás-szolgáltatóval, törölje az SQL virtuális gép *erőforrását* az Azure Portal vagy az Azure CLI használatával. Az SQL-alapú virtuális gép *erőforrásának* törlése nem törli a SQL Server VM. Azonban körültekintően járjon el, és gondosan kövesse a lépéseket, mert lehetséges, hogy véletlenül törli a virtuális gépet az *erőforrás* eltávolítására tett kísérlet során. 

Az SQL virtuális gép erőforrás-szolgáltatóval való regisztrációjának törlése szükséges a felügyeleti mód teljes körű visszalépéséhez. 

### <a name="azure-portal"></a>Azure Portal

A SQL Server VM az erőforrás-szolgáltatóval való regisztrációjának megszüntetéséhez a Azure Portal használatával hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com).
1. Navigáljon az SQL VM-erőforráshoz. 
  
   ![SQL-alapú virtuális gépek erőforrása](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Válassza a **Törlés** elemet. 

   ![Válassza a Törlés lehetőséget a felső navigációs sávon](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Írja be az SQL-virtuális gép nevét, és **törölje a virtuális gép melletti jelölőnégyzet** jelölését.

   ![Szüntesse meg a virtuális gép törlését, hogy ne törölje a tényleges virtuális gépet, majd válassza a Törlés lehetőséget az SQL VM-erőforrás törlésének folytatásához.](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Nem sikerült törölni a virtuális gép neve melletti jelölőnégyzetet a virtuális gép teljes *törléséhez* . Törölje a jelet a jelölőnégyzetből a SQL Server VM regisztrációjának törléséhez az erőforrás-szolgáltatónál, de *ne törölje a tényleges virtuális gépet*. 

1. Válassza a **Törlés** lehetőséget az SQL-alapú virtuális gép *erőforrásának* törlésének megerősítéséhez, és ne a SQL Server VM. 

### <a name="command-line"></a>Parancssor

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Ha meg szeretné szüntetni a SQL Server VM regisztrációját az erőforrás-szolgáltatóból az Azure CLI-vel, használja az az [SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást* , de nem törli a virtuális gépet. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ha meg szeretné szüntetni a SQL Server VM regisztrációját az erőforrás-szolgáltatótól a Azure PowerShell használatával, használja a [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)parancsot. Ezzel eltávolítja a SQL Server VM *erőforrást* , de nem törli a virtuális gépet. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>Következő lépések

További információkat az következő cikkekben talál: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)  
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)
