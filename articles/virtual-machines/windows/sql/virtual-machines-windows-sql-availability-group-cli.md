---
title: Azure SQL virtuális gép CLI használatával az Azure virtuális Gépeken futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása
description: 'Azure CLI használatával a Windows feladatátvevő fürt, a rendelkezésre állási csoport figyelőjének és a belső terheléselosztó létrehozása az SQL Server virtuális gépen az Azure-ban. '
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
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8af860293fc332437d67ff4db63d7686be7efff0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765271"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure SQL virtuális gép CLI használatával az Azure virtuális Gépeken futó SQL Server AlwaysOn rendelkezésre állási csoport konfigurálása
Ez a cikk ismerteti, hogyan használható [Azure SQL virtuális gép CLI](https://docs.microsoft.com/mt-mt/cli/azure/ext/sqlvm-preview/sqlvm?view=azure-cli-2018-03-01-hybrid) való üzembe helyezése a Windows feladatátvevő fürt (WSFC), és az SQL Server virtuális gépek hozzáadása a fürthöz, valamint a belső Load Balancer és a egy Always On rendelkezésre állási csoport figyelőjének létrehozásához.  Az Always On rendelkezésre állási csoport a tényleges telepítési továbbra is végezhető el manuálisan az SQL Server Management Studio (SSMS) keresztül. 

## <a name="prerequisites"></a>Előfeltételek
A telepítő egy Always On rendelkezésre állási csoport Azure SQL virtuális gép CLI-vel automatizálhatja, már rendelkeznie kell a következő előfeltételek vonatkoznak: 
- Egy [Azure-előfizetés](https://azure.microsoft.com/free/).
- Egy erőforráscsoport, egy tartományvezérlővel. 
- Legalább egy tartományhoz csatlakoztatott [virtuális gépek Azure futó SQL Server 2016 (vagy nagyobb) Enterprise verzióban](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) a a *ugyanazon rendelkezésre állási csoportban, vagy másik rendelkezésre állási zónák* amelyeket [regisztrálva az SQL virtuális gép erőforrás-szolgáltatónál](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- [Azure parancssori felület (CLI)](/cli/azure/install-azure-cli). 
- (Minden entitás által nem használt) elérhető két IP-címek, egy belső terheléselosztó, és a rendelkezésre állási csoport figyelőjének ugyanabban az alhálózatban, mint a rendelkezésre állási csoport esetében. Ha használatban van egy meglévő a terheléselosztóhoz, majd csak egy elérhető IP-cím van szükség a rendelkezésre állási csoport figyelője. 

## <a name="permissions"></a>Engedélyek
Az Always On rendelkezésre állási csoport konfigurálása Azure SQL virtuális gép CLI-vel a következő fiók engedélyek szükségesek. 

- Egy meglévő tartományi felhasználói fiók, amely "A számítógép objektum létrehozása" engedéllyel rendelkezik a tartományban.  Például egy tartományi rendszergazdai fiók általában rendelkezik megfelelő engedéllyel (például: account@domain.com). _Ez a fiók is kell a helyi rendszergazdai csoport összes virtuális gépen a fürt létrehozásához._
- A tartományi felhasználói fiók, amely az SQL Server szolgáltatás vezérli. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>1. lépés – a felhőbeli tanúsító a storage-fiók létrehozása
A fürt egy storage-fiókot, hogy működjön, a felhőbeli tanúsító van szüksége. Minden olyan meglévő tárfiókot is használhat, vagy létrehozhat egy új tárfiókot. Ha egy meglévő tárfiókot használni kívánt, folytassa a következő szakaszra. 

A következő kódrészletet a storage-fiókot hoz létre: 
```cli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > A hiba jelenhet `az sql: 'vm' is not in the 'az sql' command group` elavult verziójának használatát az Azure CLI használata. Töltse le a [Azure CLI legújabb verziójának](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) megkerülheti a hibát.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>2. lépés – a Windows feladatátvevő fürt metaadatainak megadása
Az Azure SQL virtuális gép CLI [az sql virtuális gép csoport](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) parancsot a Windows feladatátvevő fürt (WSFC) szolgáltatás, amely futtatja a rendelkezésre állási csoport metaadatainak kezel. Fürt metaadatait az Active Directory-tartománynak, a fürt fiókok, a storage-fiókok használhatók a felhőbeli tanúsító, és az SQL Server-verzió magában foglalja. Használat [az sql virtuálisgép-csoport létrehozása](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) definiálható a metaadatokat a WSFC az, hogy az első SQL Server virtuális gép hozzáadásakor, a fürt létrehozása meghatározottak szerint. 

A következő kódrészletet a fürt metaadatainak határozza meg:
```cli
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

## <a name="step-3---add-sql-server-vms-to-cluster"></a>3. lépés: az SQL Server virtuális gépek hozzáadása a fürthöz
Az első SQL Server virtuális gép felvétele a fürtbe létrehozza a fürtöt. A [az sql virtuális gép hozzáadása-az-group](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) parancs létrehozza a fürtöt a korábban megadott névvel, telepíti az SQL Server virtuális gépek a fürtszerepkör, és hozzáadja őket a fürthöz. A későbbi Használatok a `az sql vm add-to-group` parancs hozzáadja az újonnan létrehozott fürt további SQL Server rendszerű virtuális gépekhez. 

A következő kódrészlet létrehozza a fürtöt, és az első SQL Server virtuális gép hozzá: 

```cli
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
Használja ezt a parancsot a bármely más SQL Server virtuális gépek hozzáadása a fürt módosítása csak a `-n` paraméter az SQL Server rendszerű virtuális gép nevét. 

## <a name="step-4---create-availability-group"></a>4. lépés – a rendelkezésre állási csoport létrehozása
A rendelkezésre állási csoport manuálisan létrehoznia, ahogy azt szokásosan tenné, segítségével [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), vagy [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Tegye **nem** hozzon létre egy figyelő jelenleg, mert ez az alábbi szakaszok az Azure CLI-n keresztül történik.  

## <a name="step-5---create-internal-load-balancer"></a>5. lépés – belső terheléselosztó létrehozása

Az Always On rendelkezésre állási csoport (rendelkezésre állási csoport) figyelőjét egy belső Azure Load Balancer (ILB) van szükség. Az ILB a rendelkezésre állási csoport figyelőjének, amely lehetővé teszi a gyorsabb feladatátvételt és újracsatlakozás egy "nem fix" IP-címet biztosít. Ha az SQL Server virtuális gépek rendelkezésre állási csoportban az azonos rendelkezésre állási csoport részét képezik, ezt követően használhatja az alapszintű Load Balancer; Ellenkező esetben azt kell használnia a Standard Load Balancer.  **Az ILB kell lennie az SQL Server Virtuálisgép-példányok megegyező virtuális hálózatba.** 

A következő kódrészletet a belső terheléselosztót hoz létre:

```cli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > Minden egyes SQL Server rendszerű virtuális gép nyilvános IP-erőforrásból rendelkeznie kell a standard Termékváltozat a Standard Load Balancer való kompatibilitás. A virtuális gép nyilvános IP-erőforrás-Termékváltozat határozza meg, lépjen a **erőforráscsoport**, jelölje be a **nyilvános IP-cím** erőforrás számára a kívánt SQL Server virtuális Gépet, és keresse meg az értéket **Termékváltozat**  , a **áttekintése** ablaktáblán.  

## <a name="step-6---create-availability-group-listener"></a>6. lépés – a rendelkezésre állási csoport figyelőjének létrehozása
A rendelkezésre állási csoport manuális létrehozása után a figyelő használatával hozhat létre [az sql virtuális gép rendelkezésre állási csoport-figyelő](https://docs.microsoft.com/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- A **alhálózat erőforrás-azonosító** érték `/subnets/<subnetname>` hozzáfűzi a vNet-erőforrás erőforrás-Azonosítóját. Azonosítsa az alhálózat erőforrás-azonosító, tegye a következőket:
   1. Keresse meg az erőforráscsoportot, a a [az Azure portal](https://portal.azure.com). 
   1. Válassza ki a vNet-erőforrás. 
   1. Válassza ki **tulajdonságok** a a **beállítások** ablaktáblán. 
   1. Az erőforrás-azonosító, a virtuális hálózat és a hozzáfűző `/subnets/<subnetname>`végére, hogy hozzon létre az alhálózat erőforrás-azonosítója. Példa:
        - A virtuális hálózatok közötti erőforrás-azonosító van: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Alhálózat nevem `default`.
        - Ezért az alhálózat erőforrás-Azonosítómat van: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


A következő kódrészlet létrehozza a rendelkezésre állási csoport figyelőjének:

```cli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM’s hosting AG replicas ex: sqlvm1 sqlvm2>
```
## <a name="modify-number-of-replicas-in-availability-group"></a>Módosítsa a rendelkezésre állási csoport replikák száma
Van egy réteggel összetettségét is, amikor üzembe helyezése egy rendelkezésre állási csoporthoz az SQL Server virtuális gépen az Azure-ban üzemeltetett, az erőforrás-szolgáltató, és a már felügyelt erőforrások a `virtual machine group`. Mint ilyen amikor hozzáadása vagy eltávolítása a rendelkezésre állási csoport replikák, nincs egy további lépést, a figyelő metaadatok frissítését az SQL Server virtuális gépek kapcsolatos információkat. Ezért a rendelkezésre állási csoporthoz az SQL Server rendszerű virtuális gép további replikák hozzáadásakor is használnia kell a [az sqlvm aglistener hozzáadása – sqlvm](/cli/azure/ext/sqlvm-preview/sqlvm/aglistener?view=azure-cli-2018-03-01-hybrid#ext-sqlvm-preview-az-sqlvm-aglistener-add-sqlvm) parancs használatával adja hozzá az SQL Server rendszerű virtuális gép figyelő metaadat. Hasonlóképpen, a rendelkezésre állási csoport replikák eltávolításakor is használnia kell a [az sqlvm rendelkezésre állási csoport figyelőjének remove-sqlvm](/cli/azure/ext/sqlvm-preview/sqlvm/aglistener?view=azure-cli-2018-03-01-hybrid#ext-sqlvm-preview-az-sqlvm-aglistener-remove-sqlvm) , hogy az SQL Server virtuális gép metaadatainak eltávolítása a figyelőt. 

### <a name="adding-a-replica"></a>A replika hozzáadása
Új replikát a rendelkezésre állási csoport hozzáadásához tegye a következőket:

1. Az SQL Server rendszerű virtuális gép hozzáadása a fürthöz: 

    ```cli
    # Add SQL Server VM to the Cluster
    # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
    #  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

    az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
    -b <bootstrap account password> -p <operator account password> -s <service account password>
    ```
1. Az SQL Server Management Studio (SSMS) használatával adja hozzá az SQL Server-példány replikaként a rendelkezésre állási csoporton belül.
1. Adja hozzá az SQL Server rendszerű virtuális gép metaadatainak ne a figyelőt:
    ```cli
    # Add SQL VM metadata to cluster
    # example: az sqlvm aglistener add-sqlvm  --group-name Cluster`
    # --name AGListener` --resource-group SQLVM-RG `
    #--sqlvm-rid /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Compute/virtualMachines/SQLVM3
    
    az sqlvm aglistener add-sqlvm --group-name <Cluster name> `
    --name <AG Listener name> --resource-group <RG group name> `
    --sqlvm-rid <SQL VM resource ID>
    ```

### <a name="removing-a-replica"></a>Replika eltávolítása
A rendelkezésre állási csoport egy replika eltávolításához tegye a következőket:

1. Távolítsa el a replikát a rendelkezésre állási csoportból az SQL Server Management Studio (SSMS) használatával. 
1. A figyelő az SQL Server rendszerű virtuális gép metaadatainak eltávolítása:
    ```cli
    #Remove SQL VM metadata from listener
    # example: az sqlvm aglistener remove-sqlvm --group-name Cluster `
    --name AGListener` --resource-group SQLVM-RG `
    --sqlvm-rid /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Compute/virtualMachines/SQLVM3
    
    az sqlvm aglistener remove-sqlvm --group-name <Cluster name> `
    --name <AG Listener name> --resource-group <RG group name> `
    --sqlvm-rid <SQL VM resource ID>
    ``` 
1. Az SQL Server rendszerű virtuális gép eltávolítása a fürt metaadatainak:

    ```cli
    # Remove SQL VM from cluster metadata
    #example: az sqlvm remove-from-group --name SQLVM3 --resource-group SQLVM-RG
    
    az sqlvm remove from group --name <SQL VM name> --resource-group <RG name> 
    ```

## <a name="remove-availability-group-listener"></a>Távolítsa el a rendelkezésre állási csoport figyelője
Ha később el szeretné eltávolítani a rendelkezésre állási csoport figyelőjének konfigurálva az Azure CLI-vel, haladjon végig az SQL virtuális gép erőforrás-szolgáltató. A figyelő keresztül az SQL virtuális gép erőforrás-szolgáltató regisztrálva van, mivel annak csak SQL Server Management Studio segítségével törlése nem elegendő. Valóban törölni kell az Azure CLI használatával az SQL virtuális gép erőforrás-szolgáltató segítségével. Ezzel a rendelkezésre állási csoport figyelőjének metaadatok távolít el az SQL virtuális gép erőforrás-szolgáltató, és fizikailag törli a figyelőt a rendelkezésre állási csoportból. 

A következő kódrészlet törli az SQL rendelkezésre állási csoport figyelőjének, mind az SQL erőforrás-szolgáltató és a rendelkezésre állási csoportból: 

```cli
# Remove the AG listener
# example: az sqlvm aglistener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG
az sqlvm aglistener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md)
* [Az SQL Server virtuális gép – gyakori kérdések](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server rendszerű virtuális gép kibocsátási megjegyzései](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Váltás a licencmodellek szükségesek az SQL Server virtuális gép](virtual-machines-windows-sql-ahb.md)
* [Always On rendelkezésre állási csoportok áttekintése &#40;az SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Always On rendelkezésre állási csoportok a Server-példány konfigurációs &#40;az SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Egy rendelkezésre állási csoport felügyeleti &#40;az SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Rendelkezésre állási csoportok figyelési &#40;az SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Transact-SQL-utasítások az Always On rendelkezésre állási csoportok áttekintése &#40;az SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [PowerShell-parancsmagok a Always On rendelkezésre állási csoportok áttekintése &#40;az SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
