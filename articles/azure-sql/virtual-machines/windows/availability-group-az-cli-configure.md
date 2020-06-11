---
title: Rendelkezésre állási csoport konfigurálása az Azure CLI használatával
description: Az Azure CLI használatával hozza létre a Windows feladatátvevő fürtöt, a rendelkezésre állási csoport figyelőjét és a belső Load balancert egy Azure-beli SQL Server VMon.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3366438012ecc3395e7f4ae3774316ef1ddcd3b9
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669341"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-azure-vm"></a>Az Azure CLI használata az Azure-beli virtuális gépeken SQL Server always on rendelkezésre állási csoport konfigurálásához
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan használható az [Azure CLI](/cli/azure/sql/vm?view=azure-cli-latest/) egy Windows feladatátvevő fürt üzembe helyezéséhez, SQL Server virtuális gépek hozzáadásához a fürthöz, és a belső terheléselosztó és figyelő létrehozása egy always on rendelkezésre állási csoport számára. Az Always On rendelkezésre állási csoport üzembe helyezése manuálisan, SQL Server Management Studio (SSMS) keresztül történik. 

## <a name="prerequisites"></a>Előfeltételek
Az Always On rendelkezésre állási csoport Azure CLI használatával történő beállításának automatizálásához a következő előfeltételek szükségesek: 
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy tartományvezérlővel rendelkező erőforráscsoport. 
- Egy vagy több [, az Azure-ban SQL Server 2016 (vagy újabb) Enterprise Edition rendszert futtató,](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) tartományhoz csatlakoztatott virtuális gép *ugyanazon rendelkezésre állási csoporton vagy különböző rendelkezésre állási zónákban* , amelyek [regisztrálva vannak az SQL VM erőforrás-szolgáltatónál](sql-vm-resource-provider-register.md).  
- Az [Azure CLI](/cli/azure/install-azure-cli)-vel. 
- Két elérhető (egyetlen entitás sem használja) IP-címek. Az egyik a belső terheléselosztó. A másik a rendelkezésre állási csoporttal megegyező alhálózaton belüli figyelő. Meglévő terheléselosztó használata esetén csak egy elérhető IP-cím szükséges a rendelkezésre állási csoport figyelője számára. 

## <a name="permissions"></a>Engedélyek
Az Always On rendelkezésre állási csoport az Azure CLI használatával történő konfigurálásához a következő fiókra van szükség: 

- Egy meglévő tartományi felhasználói fiók, amely **számítógép-objektum létrehozása** engedéllyel rendelkezik a tartományban. Például egy tartományi rendszergazdai fióknak jellemzően megfelelő engedélye van (például: account@domain.com ). _Ennek a fióknak a helyi rendszergazda csoportnak is szerepelnie kell az egyes virtuális gépeken a fürt létrehozásához._
- A SQL Server vezérlő tartományi felhasználói fiók. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>1. lépés: Storage-fiók létrehozása Felhőbeli tanúsító
A fürtnek olyan Storage-fiókra van szüksége, amely tanúsítja a felhőt. Bármilyen meglévő Storage-fiókot használhat, vagy létrehozhat egy új Storage-fiókot is. Ha meglévő Storage-fiókot szeretne használni, ugorjon a következő szakaszra. 

A következő kódrészlet létrehozza a Storage-fiókot: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Előfordulhat, hogy a hiba akkor jelenik meg, `az sql: 'vm' is not in the 'az sql' command group` Ha az Azure CLI elavult verzióját használja. Töltse le az [Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) , hogy megszerezze a hibát.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>2. lépés: a Windows feladatátvevő fürt metaadatainak meghatározása
Az Azure CLI az [SQL VM Group](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) parancs a rendelkezésre állási csoportot üzemeltető Windows Server feladatátvételi fürt (WSFC) szolgáltatás metaadatait kezeli. A fürt metaadatai közé tartozik a Active Directory tartomány, a fürt fiókjai, a Felhőbeli tanúként használandó Storage-fiókok és a SQL Server verziója. Az az [SQL VM Group Create](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) paranccsal definiálhatja a WSFC metaadatait, hogy az első SQL Server VM hozzáadásakor a fürt a megadott módon legyen létrehozva. 

A következő kódrészlet a fürt metaadatait határozza meg:
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
A fürt első SQL Server VM hozzáadásával létrehozza a fürtöt. Az az [SQL VM-bővítmény – csoport](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) parancs létrehozza a fürtöt a korábban megadott névvel, telepíti a fürt szerepkört a SQL Server virtuális gépekre, és hozzáadja őket a fürthöz. A parancs későbbi használata `az sql vm add-to-group` további SQL Server virtuális gépeket ad hozzá az újonnan létrehozott fürthöz. 

A következő kódrészlet létrehozza a fürtöt, és hozzáadja az első SQL Server VMt: 

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
Ezzel a paranccsal adhat hozzá más SQL Server virtuális gépeket a fürthöz. Csak a `-n` paramétert módosítsa a SQL Server VM nevéhez. 

## <a name="step-4-create-the-availability-group"></a>4. lépés: a rendelkezésre állási csoport létrehozása
A szokásos módon hozza létre manuálisan a rendelkezésre állási csoportot a [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), a [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)vagy a [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql)használatával. 

>[!IMPORTANT]
> Jelenleg *ne hozzon* létre figyelőt, mert ez az alábbi részekben található Azure CLI-n keresztül történik.  

## <a name="step-5-create-the-internal-load-balancer"></a>5. lépés: a belső terheléselosztó létrehozása

Az Always On rendelkezésre állási csoport figyelője a Azure Load Balancer belső példányát igényli. A belső terheléselosztó egy "lebegő" IP-címet biztosít a rendelkezésre állási csoport figyelője számára, amely gyorsabb feladatátvételt és újracsatlakoztatást tesz lehetővé. Ha a rendelkezésre állási csoportban lévő SQL Server virtuális gépek ugyanazon rendelkezésre állási csoportba tartoznak, alapszintű Load balancert is használhat. Ellenkező esetben standard Load balancert kell használnia.  

> [!NOTE]
> A belső terheléselosztónek ugyanabban a virtuális hálózatban kell lennie, mint a SQL Server VM példányoknak. 

A következő kódrészlet létrehozza a belső terheléselosztó:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Az egyes SQL Server VMokhoz tartozó nyilvános IP-erőforrásnak standard SKU-nak kell lennie, hogy kompatibilis legyen a standard Load balancerrel. A virtuális gép nyilvános IP-címéhez tartozó SKU meghatározásához lépjen az **erőforráscsoport**elemre, válassza ki a **nyilvános IP-cím** erőforrást a kívánt SQL Server VMhoz, és keresse meg az értéket az **áttekintő** ablaktábla **SKU** elemében.  

## <a name="step-6-create-the-availability-group-listener"></a>6. lépés: a rendelkezésre állási csoport figyelő létrehozása
A rendelkezésre állási csoport manuális létrehozása után a figyelőt az [az SQL VM AG-Listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create)paranccsal hozhatja létre. 

Az *alhálózati erőforrás-azonosító* a `/subnets/<subnetname>` virtuális hálózati erőforráshoz tartozó erőforrás-azonosítóhoz hozzáfűzett érték. Az alhálózati erőforrás AZONOSÍTÓjának azonosítása:
   1. Nyissa meg az erőforráscsoportot a [Azure Portal](https://portal.azure.com). 
   1. Válassza ki a virtuális hálózati erőforrást. 
   1. A **Beállítások** ablaktáblán válassza a **Tulajdonságok** lehetőséget. 
   1. Azonosítsa a virtuális hálózat erőforrás-AZONOSÍTÓját, és fűzze hozzá a `/subnets/<subnetname>` végponthoz az alhálózati erőforrás-azonosító létrehozásához. Például:
      - A virtuális hálózati erőforrás-azonosító:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Az alhálózat neve:`default`
      - Ezért az alhálózati erőforrás-azonosító a következő:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


A következő kódrészlet hozza létre a rendelkezésre állási csoport figyelőjét:

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
Az Azure-ban üzemeltetett virtuális gépek SQL Server a rendelkezésre állási csoport üzembe helyezése során további bonyolultsági szinttel bővült. Az erőforrás-szolgáltató és a virtuálisgép-csoport most már kezeli az erőforrásokat. Így amikor replikákat ad hozzá vagy távolít el a rendelkezésre állási csoportból, a figyelő metaadatait a SQL Server virtuális gépekkel kapcsolatos információkkal együtt kell frissítenie. A rendelkezésre állási csoportban lévő replikák számának módosításakor az az [SQL VM Group AG-Listener Update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) paranccsal is frissítheti a figyelőt a SQL Server virtuális gépek metaadataival. 


### <a name="add-a-replica"></a>Replika hozzáadása

Új replika hozzáadása a rendelkezésre állási csoporthoz:

1. Adja hozzá a SQL Server VM a fürthöz:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. A SQL Server Management Studio használatával adja hozzá a SQL Server példányt replikaként a rendelkezésre állási csoporton belül.
1. Adja hozzá a SQL Server VM metaadatokat a figyelőhöz:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Replika eltávolítása

Replika eltávolítása a rendelkezésre állási csoportból:

1. Távolítsa el a replikát a rendelkezésre állási csoportból SQL Server Management Studio használatával. 
1. Távolítsa el a SQL Server VM metaadatokat a figyelőből:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Távolítsa el a SQL Server VM a fürtből:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>A rendelkezésre állási csoport figyelő eltávolítása
Ha később el kell távolítania az Azure CLI-vel konfigurált rendelkezésre állási csoport figyelőjét, el kell végeznie az SQL virtuális gép erőforrás-szolgáltatóját. Mivel a figyelő az SQL VM erőforrás-szolgáltatón keresztül van regisztrálva, csak a SQL Server Management Studio-en keresztüli törlés nem elegendő. 

A legjobb módszer az SQL VM erőforrás-szolgáltatón keresztüli Törlés az Azure CLI következő kódrészletének használatával. Ezzel eltávolítja a rendelkezésre állási csoport figyelő metaadatait az SQL VM erőforrás-szolgáltatójából. Emellett fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket: 

* [SQL Server virtuális gépek áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [SQL Server virtuális gépekkel kapcsolatos gyakori kérdések](frequently-asked-questions-faq.md)
* [SQL Server virtuális gépek kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)
* [SQL Server VM licencelési modelljeinek váltása](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Az Always On rendelkezésre állási csoportok áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Az Always On rendelkezésre állási csoportokhoz tartozó kiszolgálópéldány konfigurálása &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Rendelkezésre állási csoport felügyelete &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [A rendelkezésre állási csoportok figyelése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Az Always On rendelkezésre állási csoportokra vonatkozó Transact-SQL-utasítások áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Az Always On rendelkezésre állási csoportokhoz tartozó PowerShell-parancsmagok áttekintése &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
