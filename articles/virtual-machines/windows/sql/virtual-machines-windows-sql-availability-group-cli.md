---
title: Rendelkezésre állási csoport konfigurálása (Azure CLI)
description: Az Azure CLI használatával hozza létre a Windows feladatátvevő fürt, a rendelkezésre állási csoport figyelője, és a belső terheléselosztó egy SQL Server virtuális gép az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022361"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Az Azure CLI használatával konfigurálhatja az SQL Server mindig elérhető állási csoportját egy Azure virtuális gépen
Ez a cikk ismerteti, hogyan használhatja az [Azure CLI-t](/cli/azure/sql/vm?view=azure-cli-latest/) egy Windows feladatátvevő fürt központi telepítéséhez, sql Server virtuális gépek hozzáadásához a fürthöz, és hozza létre a belső terheléselosztót és a figyelőt egy Mindig elérhető állási csoporthoz. Az Always On rendelkezésre állási csoport telepítése továbbra is manuálisan történik az SQL Server Management Studio (SSMS) segítségével. 

## <a name="prerequisites"></a>Előfeltételek
Az Azure CLI használatával az Always On rendelkezésre állási csoport beállításának automatizálásához a következő előfeltételekkel kell rendelkeznie: 
- [Egy Azure-előfizetés](https://azure.microsoft.com/free/).
- Erőforráscsoport tartományvezérlővel. 
- Egy vagy több tartományhoz csatlakozó virtuális gép az [Azure-ban, amely EN-alapú SQL Server 2016 (vagy újabb) Enterprise edition szolgáltatást futtat](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) ugyanazon a rendelkezésre állási készletben vagy az SQL [virtuálisgép-erőforrás-szolgáltatónál regisztrált](virtual-machines-windows-sql-register-with-resource-provider.md)különböző *rendelkezésre állási zónákban.*  
- Az [Azure CLI](/cli/azure/install-azure-cli). 
- Két rendelkezésre álló (egyetlen entitás által nem használt) IP-cím. Az egyik a belső terheléselosztó. A másik a rendelkezésre állási csoport figyelő ugyanazon az alhálózaton belül, mint a rendelkezésre állási csoport. Ha egy meglévő terheléselosztót használ, csak egy elérhető IP-címre van szüksége a rendelkezésre állási csoport figyelőjéhez. 

## <a name="permissions"></a>Engedélyek
Az Azure CLI használatával a következő fiókengedélyekre van szükség az Always On availability csoport konfigurálásához: 

- Olyan meglévő tartományi felhasználói fiók, amely **számítógépobjektum-létrehozási** engedéllyel rendelkezik a tartományban. Például egy tartományi rendszergazdai fiók általában megfelelő account@domain.comengedéllyel rendelkezik (például: ). _Ez a fiók is része kell lennie a helyi rendszergazdai csoport minden virtuális gép a fürt létrehozásához._
- Az SQL Server szolgáltatást vezérlő tartományi felhasználói fiók. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>1. lépés: Tárfiók létrehozása felhőbeli tanúsítóként
A fürtnek szüksége van egy tárfiókra, hogy a felhő tanúsítóként működjön. Használhatja a meglévő tárfiókot, vagy létrehozhat egy új tárfiókot. Ha meglévő tárfiókot szeretne használni, ugorjon előre a következő szakaszra. 

A következő kódrészlet létrehozza a tárfiókot: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Előfordulhat, hogy `az sql: 'vm' is not in the 'az sql' command group` a hiba, ha az Azure CLI elavult verzióját használja. Töltse le az [Azure CLI legújabb verzióját,](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) hogy túljusson ezen a hibán.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>2. lépés: A Windows feladatátvevő fürt metaadatainak meghatározása
Az Azure CLI [az sql vm csoport](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) parancscsoportja kezeli a rendelkezésre állási csoportot kiszolgáló WSFC szolgáltatást tartalmazó szolgáltatás metaadatait. A fürt metaadatai közé tartozik az Active Directory-tartomány, a fürtfiókok, a felhőtanúsítóként használandó tárfiókok és az SQL Server verziója. Az [sql vm csoport létrehozása](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) segítségével határozza meg a WSFC metaadatait, így az első SQL Server virtuális gép hozzáadásakor a fürt a meghatározott ként jön létre. 

A következő kódrészlet határozza meg a fürt metaadatait:
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>3. lépés: SQL Server virtuális gépek hozzáadása a fürthöz
Az első SQL Server virtuális gép hozzáadása a fürthöz létrehozza a fürtöt. Az [az sql vm add-to-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) parancs a fürtöt a korábban megadott névvel hozza létre, telepíti a fürtszerepkört az SQL Server virtuális gépekre, és hozzáadja őket a fürthöz. A `az sql vm add-to-group` parancs későbbi felhasználásai további SQL Server virtuális gépeket adnak az újonnan létrehozott fürthöz. 

A következő kódrészlet létrehozza a fürtöt, és hozzáadja az első SQL Server virtuális géphez: 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Ezzel a paranccsal más SQL Server virtuális gépeket is hozzáadhat a fürthöz. Csak az `-n` SQL Server virtuálisgép nevének paraméterét módosítsa. 

## <a name="step-4-create-the-availability-group"></a>4. lépés: A rendelkezésre állási csoport létrehozása
Manuálisan hozza létre a rendelkezésre állási csoportot a szokásos módon az [SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)a [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)vagy [a Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)használatával. 

>[!IMPORTANT]
> *Ne* hozzon létre egy figyelő t, mert ez az Azure CLI-n keresztül történik a következő szakaszokban.  

## <a name="step-5-create-the-internal-load-balancer"></a>5. lépés: A belső terheléselosztó létrehozása

A Mindig rendelkezésre álláson lévő csoportfigyelő az Azure Load Balancer belső példányát igényli. A belső terheléselosztó egy "lebegő" IP-címet biztosít a rendelkezésre állási csoport figyelőjének, amely lehetővé teszi a gyorsabb feladatátvételt és újracsatlakozást. Ha egy rendelkezésre állási csoportban lévő SQL Server virtuális gépek ugyanannak a rendelkezésre állási csoportnak a részét képezik, használhatja az alapszintű terheléselosztót. Ellenkező esetben szabványos terheléselosztót kell használnia.  

> [!NOTE]
> A belső terheléselosztónak ugyanabban a virtuális hálózatban kell lennie, mint az SQL Server virtuálisgép-példányainak. 

A következő kódrészlet hozza létre a belső terheléselosztót:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Az egyes SQL Server virtuális gépek nyilvános IP-erőforrásának szabványos termékváltozattal kell rendelkeznie, hogy kompatibilis legyen a standard terheléselosztóval. A virtuális gép nyilvános IP-erőforrásának termékváltozatának meghatározásához nyissa meg az **Erőforráscsoport**lehetőséget, válassza ki a kívánt SQL Server virtuális gép **nyilvános IP-cím erőforrását,** és keresse meg az értéket a **Termékváltozat** ban az **Áttekintő** ablaktáblában.  

## <a name="step-6-create-the-availability-group-listener"></a>6. lépés: A rendelkezésre állási csoport figyelőjének létrehozása
Miután manuálisan létrehozta a rendelkezésre állási csoportot, létrehozhatja a figyelőt az [sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)használatával. 

Az *alhálózati erőforrásazonosító* a `/subnets/<subnetname>` virtuális hálózati erőforrás erőforrásazonosítójához hozzáfűzve megadott érték. Az alhálózati erőforrás azonosítójának azonosítása:
   1. Nyissa meg az erőforráscsoportot az [Azure Portalon.](https://portal.azure.com) 
   1. Válassza ki a virtuális hálózati erőforrást. 
   1. A **Beállítások** ablaktáblán válassza a **Tulajdonságok** lehetőséget. 
   1. Azonosítsa a virtuális hálózat erőforrás-azonosítóját, és fűzz `/subnets/<subnetname>` hozzá az alhálózati erőforrásazonosító létrehozásához. Példa:
      - A virtuális hálózati erőforrás azonosítója:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Az alhálózat neve:`default`
      - Ezért az alhálózati erőforrásazonosító:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


A következő kódrészlet létrehozza a rendelkezésre állási csoport figyelője:

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Egy rendelkezésre állási csoportban lévő replikák számának módosítása
Van egy hozzáadott összetettségi réteg, amikor egy rendelkezésre állási csoportot telepít az Azure-ban üzemeltetett SQL Server virtuális gépekre. Az erőforrás-szolgáltató és a virtuálisgép-csoport most kezeli az erőforrásokat. Így a rendelkezésre állási csoportreplikák hozzáadásakor vagy eltávolításakor a figyelő metaadatainak frissítése további lépés az SQL Server virtuális gépekre vonatkozó információkkal. A rendelkezésre állási csoportban lévő replikák számának módosításakor az [az sql vm csoport ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) paranccsal is frissítenie kell a figyelőt az SQL Server virtuális gépek metaadataival. 


### <a name="add-a-replica"></a>Kópia hozzáadása

Új kópia hozzáadása az elérhetőségi csoporthoz:

1. Adja hozzá az SQL Server virtuális gépét a fürthöz:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Az SQL Server Management Studio segítségével adja hozzá az SQL Server-példányt kópiaként a rendelkezésre állási csoporton belül.
1. Adja hozzá az SQL Server virtuális gép metaadatait a figyelőhöz:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Kópia eltávolítása

Kópia eltávolítása az elérhetőségi csoportból:

1. Távolítsa el a replikát az sql Server Management Studio használatával az elérhetőségi csoportból. 
1. Távolítsa el az SQL Server virtuálisgép metaadatait a figyelőből:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Az SQL Server virtuális gép eltávolítása a fürtből:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Az elérhetőségi csoport figyelőjének eltávolítása
Ha később el kell távolítania az Azure CLI-vel konfigurált rendelkezésre állási csoportfigyelőt, át kell mennie az SQL virtuálisgép-erőforrás-szolgáltatón. Mivel a figyelő regisztrálva van az SQL Virtuálisgép-erőforrás-szolgáltatón keresztül, csak az SQL Server Management Studio-n keresztüli törlése nem elegendő. 

A legjobb módszer az SQL virtuálisgép-erőforrás-szolgáltatón keresztül i. az Azure CLI-ben a következő kódrészlet használatával törölni. Ezzel eltávolítja a rendelkezésre állási csoport figyelőmetaadatait az SQL virtuálisgép-erőforrás-szolgáltatóból. Azt is fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server virtuális gépek áttekintése](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server virtuális gépekhez](virtual-machines-windows-sql-server-iaas-faq.md)
* [Kibocsátási megjegyzések az SQL Server virtuális gépekhez](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Licencelési modellek váltása SQL Server virtuális géphez](virtual-machines-windows-sql-ahb.md)
* [A Mindig rendelkezésre állási csoportok áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Kiszolgálópéldány konfigurálása az SQL Server &#40;&#41;mindig bekapcsolva beállítási csoportokhoz](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Az SQL Server&#41;&#40;rendelkezésre állási csoport felügyelete](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [A rendelkezésre állási csoportok figyelése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Az Always On rendelkezésre állási csoportok &#40;az SQL Server&#41;Transact-SQL utasítások áttekintése](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [A PowerShell-parancsmagok áttekintése az SQL Server &#40;mindig rendelkezésre állási csoportokhoz&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
