---
title: Klasszikus Azure CLI-parancsok
description: Az Azure parancssori felület (CLI) parancsai az erőforrások kezeléséhez.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/18/2017
ms.author: cynthn
ms.openlocfilehash: 5b7d6f4efa4a910d3141638602c603d484fac6da
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86181862"
---
# <a name="azure-classic-cli-commands"></a>Klasszikus Azure CLI-parancsok 

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Ez a témakör az Azure klasszikus parancssori felületének telepítését ismerteti. A klasszikus parancssori felület elavult, és csak a klasszikus üzemi modellel használható. Az összes többi központi telepítéshez használja az [Azure CLI](https://docs.microsoft.com/cli/azure/)-t.

Ez a cikk a klasszikus Azure parancssori felület (CLI) azon parancsainak szintaxisát és beállításait ismerteti, amelyeket általában Azure-erőforrások létrehozására és kezelésére használhat. Ez nem egy teljes hivatkozás, és a CLI-verziója némileg eltérő parancsokat vagy paramétereket is megjeleníthet. 

Első lépésként [telepítse a klasszikus Azure CLI](../cli-install-nodejs.md) -t, és [kapcsolódjon az Azure-előfizetéséhez](/cli/azure/authenticate-azure-cli).

Az aktuális parancs szintaxisa és a parancssorban a Resource Manager módban írja be a (z) vagy parancsot, `azure help` Ha egy adott parancs súgóját szeretné megjeleníteni `azure help [command]` . Az adott Azure-szolgáltatások létrehozásához és kezeléséhez használt dokumentációban is található CLI-példák.

A választható paraméterek szögletes zárójelben jelennek meg (például: `[parameter]` ). Minden egyéb paraméter megadása kötelező.

Az itt dokumentált parancs-specifikus paraméterek mellett három opcionális paraméter is használható a részletes kimenet, például a kérelmek és az állapotkódok megjelenítéséhez. A `-v` paraméter részletes kimenetet biztosít, és a `-vv` paraméter még részletesebb részletes kimenetet biztosít. A `--json` kapcsoló nyers JSON formátumban jeleníti meg az eredményt.

## <a name="setting-the-resource-manager-mode"></a>A Resource Manager üzemmód beállítása
Az alábbi parancs használatával engedélyezheti az Azure CLI Resource Manager üzemmód parancsait.

```azurecli
azure config mode arm
```

> [!NOTE]
> A CLI Azure Resource Manager mód és az Azure Service Management mód kölcsönösen kizárják egymást. Ez azt eredményezi, hogy az egyik módban létrehozott erőforrások nem kezelhetők a másik módból.
>


## <a name="account-information"></a>Fiók adatai
Az eszköz az Azure-előfizetési adatokat használja a fiókjához való kapcsolódáshoz.

**Az importált előfizetések listázása**

```azurecli
account list [options]
```

**Előfizetés részleteinek megjelenítése**  

```azurecli
account show [options] [subscriptionNameOrId]
```

**Az aktuális előfizetés beállítása**
```azurecli
account set [options] <subscriptionNameOrId>
```

**Előfizetés vagy környezet eltávolítása, illetve az összes tárolt fiók és környezeti információ törlése**  
```azurecli
account clear [options]
```

**A fiók környezetét kezelő parancsok**  

```azurecli
account env list [options]
account env show [options] [environment]
account env add [options] [environment]
account env set [options] [environment]
account env delete [options] [environment]
```

## <a name="active-directory-objects"></a>Objektumok Active Directory
**Az Active Directory-alkalmazások megjelenítésére szolgáló parancsok**

```azurecli
ad app create [options]
ad app delete [options] <object-id>
```

**Az Active Directory-csoportok megjelenítésére szolgáló parancsok**

```azurecli
ad group list [options]
ad group show [options]
```

**Az Active Directory alcsoportot vagy a tagok adatait biztosító parancsok**

```azurecli
ad group member list [options] [objectId]
```

**Az Active Directory egyszerű szolgáltatásait megjelenítő parancsok**

```azurecli
ad sp list [options]
ad sp show [options]
ad sp create [options] <application-id>
ad sp delete [options] <object-id>
```

**Az Active Directory-felhasználók megjelenítésére szolgáló parancsok**

```azurecli
ad user list [options]
ad user show [options]
```

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
**Rendelkezésre állási csoport létrehozása egy erőforráscsoporthoz**

```azurecli
availset create [options] <resource-group> <name> <location> [tags]
```

**Az erőforráscsoporthoz tartozó rendelkezésre állási csoportok felsorolása**

```azurecli
availset list [options] <resource-group>
```

**Egy erőforráscsoport egy rendelkezésre állási csoportjának beolvasása**

```azurecli
availset show [options] <resource-group> <name>
```

**Egy erőforráscsoport összes rendelkezésre állási csoportjának törlése**

```azurecli
availset delete [options] <resource-group> <name>
```

## <a name="local-settings"></a>Helyi beállítások
**Az Azure CLI konfigurációs beállításainak listázása**

```azurecli
config list [options]
```

**Konfigurációs beállítás törlése**

```azurecli
config delete [options] <name>
```

**Konfigurációs beállítás frissítése**

```azurecli
config set <name> <value>
```

**Beállítja az Azure CLI munkamódszerét `arm` vagy`asm`**

```azurecli
config mode [options] <modename>
```


## <a name="account-features"></a>Fiók funkciói
**Az előfizetéshez elérhető összes funkció listázása**

```azurecli
feature list [options]
```

**Egy szolgáltatás megjelenítése**

```azurecli
feature show [options] <providerName> <featureName>
```

**Egy erőforrás-szolgáltató előzetesen megtekintett funkciójának regisztrálása**

```azurecli
feature register [options] <providerName> <featureName>
```

## <a name="resource-groups"></a>Erőforráscsoportok
**Létrehoz egy erőforráscsoportot**

```azurecli
group create [options] <name> <location>
```

**Címkék beállítása erőforráscsoporthoz**

```azurecli
group set [options] <name> <tags>
```

**Erőforráscsoport törlése**

```azurecli
group delete [options] <name>
```

**Az előfizetéshez tartozó erőforráscsoportok listája**

```azurecli
group list [options]
```

**Az előfizetéshez tartozó erőforráscsoport megjelenítése**

```azurecli
group show [options] <name>
```

**Az erőforráscsoport-naplók kezelésére szolgáló parancsok**

```azurecli
group log show [options] [name]
```

**Az üzemelő példányok erőforráscsoporthoz való felügyeletére szolgáló parancsok**

```azurecli
group deployment create [options] [resource-group] [name]
group deployment list [options] <resource-group> [state]
group deployment show [options] <resource-group> [deployment-name]
group deployment stop [options] <resource-group> [deployment-name]
```

**A helyi vagy a katalógus erőforráscsoport-sablonjának kezelésére szolgáló parancsok**

```azurecli
group template list [options]
group template show [options] <name>
group template download [options] [name] [file]
group template validate [options] <resource-group>
```

## <a name="hdinsight-clusters"></a>HDInsight-fürtök
**A fürt konfigurációs fájljának létrehozásához vagy hozzáadásához szükséges parancsok**

```azurecli
hdinsight config create [options] <configFilePath> <overwrite>
hdinsight config add-config-values [options] <configFilePath>
hdinsight config add-script-action [options] <configFilePath>
```


Példa: hozzon létre egy konfigurációs fájlt, amely tartalmazza a fürt létrehozásakor futtatandó parancsfájl-műveletet.

```azurecli
hdinsight config create "C:\myFiles\configFile.config"
hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"
```

**Parancs fürt létrehozásához egy erőforráscsoporthoz**

```azurecli
hdinsight cluster create [options] <clusterName>
```

Példa: Storm létrehozása Linux-fürtön

```azurecli
azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Storm --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 myNewCluster01

info:    Executing command hdinsight cluster create
+ Submitting the request to create cluster...
info:    hdinsight cluster create command OK
```

Példa: hozzon létre egy fürtöt parancsfájl-művelettel.

```azurecli
azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01

info:    Executing command hdinsight cluster create
+ Submitting the request to create cluster...
info:    hdinsight cluster create command OK
```

Paraméter beállításai:

```txt
-h, --help                                                 output usage information
-v, --verbose                                              use verbose output
-vv                                                        more verbose with debug output
--json                                                     use json output
-g --resource-group <resource-group>                       The name of the resource group
-c, --clusterName <clusterName>                            HDInsight cluster name
-l, --location <location>                                  Data center location for the cluster
-y, --osType <osType>                                      HDInsight cluster operating system
'Windows' or 'Linux'
--version <version>                                        HDInsight cluster version
--clusterType <clusterType>                                HDInsight cluster type.
Hadoop | HBase | Spark | Storm
--defaultStorageAccountName <storageAccountName>           Storage account url to use for default HDInsight storage
--defaultStorageAccountKey <storageAccountKey>             Key to the storage account to use for default HDInsight storage
--defaultStorageContainer <storageContainer>               Container in the storage account to use for HDInsight default storage
--headNodeSize <headNodeSize>                              (Optional) Head node size for the cluster
--workerNodeCount <workerNodeCount>                        Number of worker nodes to use for the cluster
--workerNodeSize <workerNodeSize>                          (Optional) Worker node size for the cluster)
--zookeeperNodeSize <zookeeperNodeSize>                    (Optional) Zookeeper node size for the cluster
--userName <userName>                                      Cluster username
--password <password>                                      Cluster password
--sshUserName <sshUserName>                                SSH username (only for Linux clusters)
--sshPassword <sshPassword>                                SSH password (only for Linux clusters)
--sshPublicKey <sshPublicKey>                              SSH public key (only for Linux clusters)
--rdpUserName <rdpUserName>                                RDP username (only for Windows clusters)
--rdpPassword <rdpPassword>                                RDP password (only for Windows clusters)
--rdpAccessExpiry <rdpAccessExpiry>                        RDP access expiry.
For example 12/12/2015 (only for Windows clusters)
--virtualNetworkId <virtualNetworkId>                      (Optional) Virtual network ID for the cluster.
Value is a GUID for Windows cluster and ARM resource ID for Linux cluster)
--subnetName <subnetName>                                  (Optional) Subnet for the cluster
--additionalStorageAccounts <additionalStorageAccounts>    (Optional) Additional storage accounts.
Can be multiple.
In the format of 'accountName#accountKey'.
For example, --additionalStorageAccounts "acc1#key1;acc2#key2"
--hiveMetastoreServerName <hiveMetastoreServerName>        (Optional) SQL Server name for the external metastore for Hive
--hiveMetastoreDatabaseName <hiveMetastoreDatabaseName>    (Optional) Database name for the external metastore for Hive
--hiveMetastoreUserName <hiveMetastoreUserName>            (Optional) Database username for the external metastore for Hive
--hiveMetastorePassword <hiveMetastorePassword>            (Optional) Database password for the external metastore for Hive
--oozieMetastoreServerName <oozieMetastoreServerName>      (Optional) SQL Server name for the external metastore for Oozie
--oozieMetastoreDatabaseName <oozieMetastoreDatabaseName>  (Optional) Database name for the external metastore for Oozie
--oozieMetastoreUserName <oozieMetastoreUserName>          (Optional) Database username for the external metastore for Oozie
--oozieMetastorePassword <oozieMetastorePassword>          (Optional) Database password for the external metastore for Oozie
--configurationPath <configurationPath>                    (Optional) HDInsight cluster configuration file path
-s, --subscription <id>                                    The subscription id
--tags <tags>                                              Tags to set to the cluster.
Can be multiple.
In the format of 'name=value'.
Name is required and value is optional.
For example, --tags tag1=value1;tag2
```


**A fürt törlésére szolgáló parancs**

```azurecli
hdinsight cluster delete [options] <clusterName>
```

**Parancs a fürt részleteinek megjelenítéséhez**

```azurecli
hdinsight cluster show [options] <clusterName>
```

**Parancs az összes fürt listázásához (adott erőforráscsoporthoz, ha meg van adni)**

```azurecli
hdinsight cluster list [options]
```

**A fürt átméretezésére szolgáló parancs**

```azurecli
hdinsight cluster resize [options] <clusterName> <targetInstanceCount>
```

**A fürt HTTP-hozzáférésének engedélyezésére szolgáló parancs**

```azurecli
hdinsight cluster enable-http-access [options] <clusterName> <userName> <password>
```

**A fürt HTTP-hozzáférésének letiltására szolgáló parancs**

```azurecli
hdinsight cluster disable-http-access [options] <clusterName>
```

**A fürt RDP-hozzáférésének engedélyezésére szolgáló parancs**

```azurecli
hdinsight cluster enable-rdp-access [options] <clusterName> <rdpUserName> <rdpPassword> <rdpExpiryDate>
```

**A fürt HTTP-hozzáférésének letiltására szolgáló parancs**

```azurecli
hdinsight cluster disable-rdp-access [options] <clusterName>
```

## <a name="insights-events-alert-rules-autoscale-settings-metrics"></a>Bepillantások (események, riasztási szabályok, autoskálázási beállítások, metrikák)
**Egy előfizetéshez, correlationId, erőforráscsoporthoz, erőforráshoz vagy erőforrás-szolgáltatóhoz tartozó műveleti naplók beolvasása**

```azurecli
insights logs list [options]
```

## <a name="locations"></a>Helyek 
**Az elérhető helyszínek listázása**

```azurecli
location list [options]
```

## <a name="network-resources"></a>Hálózati erőforrások
**Virtuális hálózatok kezelésére szolgáló parancsok**

```azurecli
network vnet create [options] <resource-group> <name> <location>
```
Virtuális hálózatot hoz létre. A következő példában létrehozunk egy newvnet nevű virtuális hálózatot az erőforráscsoport myresourcegroup az USA nyugati régiójában.

```azurecli
azure network vnet create myresourcegroup newvnet "west us"
info:    Executing command network vnet create
+ Looking up virtual network "newvnet"
+ Creating virtual network "newvnet"
    Loading virtual network state
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet create command OK
```


Paraméter beállításai:

```txt
-h, --help                                 output usage information
-v, --verbose                              use verbose output
--json                                     use json output
-g, --resource-group <resource-group>      the name of the resource group
-n, --name <name>                          the name of the virtual network
-l, --location <location>                  the location
-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network
For example -a 10.0.0.0/24,10.0.1.0/24.
Default value is 10.0.0.0/8

-d, --dns-servers <dns-servers>            the comma separated list of DNS servers IP addresses
-t, --tags <tags>                          the tags set on this virtual network.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>          the subscription identifier
```
<BR>

```azurecli
network vnet set [options] <resource-group> <name>
```

Egy erőforráscsoport virtuális hálózati konfigurációjának frissítése.

```azurecli
azure network vnet set myresourcegroup newvnet

info:    Executing command network vnet set
+ Looking up virtual network "newvnet"
+ Updating virtual network "newvnet"
+ Loading virtual network state
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet set command OK
```

Paraméter beállításai:

```txt
-h, --help                                 output usage information
-v, --verbose                              use verbose output
--json                                     use json output
-g, --resource-group <resource-group>      the name of the resource group
-n, --name <name>                          the name of the virtual network
-a, --address-prefixes <address-prefixes>  the comma separated list of address prefixes for this virtual network.
For example -a 10.0.0.0/24,10.0.1.0/24.
This list will be appended to the current list of address prefixes.
The address prefixes in this list should not overlap between them.
The address prefixes in this list should not overlap with existing address prefixes in the vnet.

-d, --dns-servers [dns-servers]            the comma separated list of DNS servers IP addresses.
This list will be appended to the current list of DNS server IP addresses.

-t, --tags <tags>                          the tags set on this virtual network.
Can be multiple. In the format of "name=value".
Name is required and value is optional. For example, -t tag1=value1;tag2.
This list will be appended to the current list of tags

--no-tags                                  remove all existing tags
-s, --subscription <subscription>          the subscription identifier
```
<BR>

```azurecli
network vnet list [options] <resource-group>
```

A parancs az erőforráscsoport összes virtuális hálózatát listázza.

```azurecli
C:\>azure network vnet list myresourcegroup

info:    Executing command network vnet list
+ Listing virtual networks
    data:    ID
    Name      Location  Address prefixes  DNS servers
data:    -------------------------------------------------------------------
------  --------  --------  ----------------  -----------
data:    /subscriptions/###############################/resourceGroups/
wvnet   newvnet   westus    10.0.0.0/8
info:    network vnet list command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network vnet show [options] <resource-group> <name>
```
A parancs egy erőforráscsoport virtuális hálózatának tulajdonságait jeleníti meg.

```azurecli
azure network vnet show -g myresourcegroup -n newvnet

info:    Executing command network vnet show
+ Looking up virtual network "newvnet"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet
data:    Name:                 newvnet
data:    Type:                 Microsoft.Network/virtualNetworks
data:    Location:             westus
data:    Tags:
data:    Provisioning state:   Succeeded
data:    Address prefixes:
data:     10.0.0.0/8
data:    DNS servers:
data:    Subnets:
data:
info:    network vnet show command OK
```
<BR>

```azurecli
network vnet delete [options] <resource-group> <name>
```
A parancs eltávolítja a virtuális hálózatot.

```azurecli
azure network vnet delete myresourcegroup newvnetX

info:    Executing command network vnet delete
+ Looking up virtual network "newvnetX"
Delete virtual network newvnetX? [y/n] y
+ Deleting virtual network "newvnetX"
info:    network vnet delete command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the virtual network
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```


**Virtuális hálózati alhálózatok kezelésére szolgáló parancsok**

```azurecli
network vnet subnet create [options] <resource-group> <vnet-name> <name>
```

Egy másik alhálózatot rendel egy meglévő virtuális hálózathoz.

```azurecli
azure network vnet subnet create -g myresourcegroup --vnet-name newvnet -n subnet --address-prefix 10.0.1.0/24

info:    Executing command network vnet subnet create
+ Looking up the subnet "subnet"
+ Creating subnet "subnet"
+ Looking up the subnet "subnet"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
data:    Name:                      subnet
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet create command OK
```

Paraméter beállításai:

```txt
-h, --help                                                       output usage information
-v, --verbose                                                    use verbose output
    --json                                                           use json output
-g, --resource-group <resource-group>                            the name of the resource group
-e, --vnet-name <vnet-name>                                      the name of the virtual network
-n, --name <name>                                                the name of the subnet
-a, --address-prefix <address-prefix>                            the address prefix
-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
    e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
-o, --network-security-group-name <network-security-group-name>  the network security group name
-s, --subscription <subscription>                                the subscription identifier
```

<BR>

```azurecli
network vnet subnet set [options] <resource-group> <vnet-name> <name>
```

Egy erőforráscsoport adott virtuális hálózati alhálózatának beállítása.

```azurecli
C:\>azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet set
+ Looking up the subnet "subnet1"
+ Setting subnet "subnet1"
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet set command OK
```
<BR>

```azurecli
network vnet subnet list [options] <resource-group> <vnet-name>
```

Az erőforráscsoport egy adott virtuális hálózatának összes virtuális hálózati alhálózatát listázza.

```azurecli
azure network vnet subnet set -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet set
+ Looking up the subnet "subnet1"
+ Setting subnet "subnet1"
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet set command OK
```
<BR>

```azurecli
network vnet subnet show [options] <resource-group> <vnet-name> <name>
```
Virtuális hálózati alhálózat tulajdonságainak megjelenítése

```azurecli
azure network vnet subnet show -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet show
+ Looking up the subnet "subnet1"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft
.Network/virtualNetworks/newvnet/subnets/subnet1
data:    Name:                      subnet1
data:    Type:                      Microsoft.Network/virtualNetworks/subnets
data:    Provisioning state:        Succeeded
data:    Address prefix:            10.0.1.0/24
info:    network vnet subnet show command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-e, --vnet-name <vnet-name>            the name of the virtual network
-n, --name <name>                      the name of the subnet
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
```
Eltávolít egy alhálózatot egy meglévő virtuális hálózatból.

```azurecli
azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

info:    Executing command network vnet subnet delete
+ Looking up the subnet "subnet1"
Delete subnet "subnet1"? [y/n] y
+ Deleting subnet "subnet1"
info:    network vnet subnet delete command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-e, --vnet-name <vnet-name>            the name of the virtual network
-n, --name <name>                      the subnet name
-s, --subscription <subscription>      the subscription identifier
-q, --quiet                            quiet mode, do not ask for delete confirmation
```

**A terheléselosztó kezelésére szolgáló parancsok**

```azurecli
network lb create [options] <resource-group> <name> <location>
```
Létrehoz egy terheléselosztó-készletet.

```azurecli
azure network lb create -g myresourcegroup -n mylb -l westus

info:    Executing command network lb create
+ Looking up the load balancer "mylb"
+ Creating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
data:    Name:                         mylb
data:    Type:                         Microsoft.Network/loadBalancers
data:    Location:                     westus
data:    Provisioning state:           Succeeded
info:    network lb create command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-l, --location <location>              the location
-t, --tags <tags>                      the list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional. For example, -t tag1=value1;tag2
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb list [options] <resource-group>
```
A terheléselosztó erőforrásait sorolja fel egy erőforráscsoporthoz.

```azurecli
azure network lb list myresourcegroup

info:    Executing command network lb list
+ Getting the load balancers
data:    Name  Location
data:    ----  --------
data:    mylb  westus
info:    network lb list command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb show [options] <resource-group> <name>
```

Egy adott terheléselosztó terheléselosztási információit jeleníti meg egy adott erőforráson belül

```azurecli
azure network lb show myresourcegroup mylb -v

info:    Executing command network lb show
verbose: Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
data:    Name:                         mylb
data:    Type:                         Microsoft.Network/loadBalancers
data:    Location:                     westus
data:    Provisioning state:           Succeeded
info:    network lb show command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb delete [options] <resource-group> <name>
```

A terheléselosztó erőforrásainak törlése.

```azurecli
azure network lb delete  myresourcegroup mylb

info:    Executing command network lb delete
+ Looking up the load balancer "mylb"
Delete load balancer "mylb"? [y/n] y
+ Deleting load balancer "mylb"
info:    network lb delete command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the load balancer
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**A Load Balancer szondáinak kezelésére szolgáló parancsok**

```azurecli
network lb probe create [options] <resource-group> <lb-name> <name>
```

Hozza létre a mintavételi konfigurációt az állapothoz a terheléselosztó számára. Ne feledje, hogy a parancs futtatásához a terheléselosztó egy előtér-IP-erőforrást igényel (az "Azure Network frontend-IP" paranccsal rendeljen hozzá egy IP-címet a terheléselosztó számára).

```azurecli
azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

info:    Executing command network lb probe create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
info:    network lb probe create command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the probe
-p, --protocol <protocol>              the probe protocol
-o, --port <port>                      the probe port
-f, --path <path>                      the probe path
-i, --interval <interval>              the probe interval in seconds
-c, --count <count>                    the number of probes
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb probe set [options] <resource-group> <lb-name> <name>
```

Frissíti egy meglévő terheléselosztó-mintavételt új értékekkel.

```azurecli
azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

info:    Executing command network lb probe set
    + Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
info:    network lb probe set command OK
```

Paraméterek beállításai

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the probe
-e, --new-probe-name <new-probe-name>  the new name of the probe
-p, --protocol <protocol>              the new value for probe protocol
-o, --port <port>                      the new value for probe port
-f, --path <path>                      the new value for probe path
-i, --interval <interval>              the new value for probe interval in seconds
-c, --count <count>                    the new value for number of probes
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb probe list [options] <resource-group> <lb-name>
```

A terheléselosztó-készlet mintavételi tulajdonságainak listázása.

```azurecli
C:\>azure network lb probe list -g myresourcegroup -l mylb

info:    Executing command network lb probe list
+ Looking up the load balancer "mylb"
data:    Name       Protocol  Port  Path  Interval  Count
data:    ---------  --------  ----  ----  --------  -----
data:    mylbprobe  Tcp       443         300       2
info:    network lb probe list command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

```azurecli
network lb probe delete [options] <resource-group> <lb-name> <name>
```
Eltávolítja a terheléselosztó számára létrehozott mintavételt.

```azurecli
azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

info:    Executing command network lb probe delete
+ Looking up the load balancer "mylb"
Delete a probe "mylbprobe?" [y/n] y
+ Updating load balancer "mylb"
info:    network lb probe delete command OK
```

**Parancsok a terheléselosztó előtér-IP-konfigurációinak kezeléséhez**

```azurecli
network lb frontend-ip create [options] <resource-group> <lb-name> <name>
```
Előtérbeli IP-konfigurációt hoz létre egy meglévő terheléselosztó-készlethez.

```azurecli
azure network lb frontend-ip create -g myresourcegroup --lb-name mylb -n myfrontendip -o Dynamic -e subnet -m newvnet

info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "mylb"
+ Looking up the subnet "subnet"
+ Creating frontend IP configuration "myfrontendip"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/Myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
/frontendIPConfigurations/myfrontendip
data:    Name:                         myfrontendip
data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
data:    Provisioning state:           Succeeded
data:    Private IP allocation method: Dynamic
data:    Private IP address:           10.0.1.4
data:    Subnet:                       id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/newvnet/subnets/subnet
data:    Public IP address:
data:    Inbound NAT rules
data:    Outbound NAT rules
data:    Load balancing rules
data:
info:    network lb frontend-ip create command OK
```

<BR>

```azurecli
network lb frontend-ip set [options] <resource-group> <lb-name> <name>
```

Frissíti egy előtérbeli IP-cím meglévő konfigurációját. Az alábbi parancs egy mypubip5 nevű nyilvános IP-címet hoz létre egy meglévő, myfrontendip nevű Load Balancer-előtérbeli IP-címhez.

```azurecli
azure network lb frontend-ip set -g myresourcegroup --lb-name mylb -n myfrontendip -i mypubip5

info:    Executing command network lb frontend-ip set
+ Looking up the load balancer "mylb"
+ Looking up the public ip "mypubip5"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Name:                         myfrontendip
data:    Type:                         Microsoft.Network/loadBalancers/frontendIPConfigurations
data:    Provisioning state:           Succeeded
data:    Private IP allocation method: Dynamic
data:    Private IP address:
data:    Subnet:
data:    Public IP address:            id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypubip5
data:    Inbound NAT rules
data:    Outbound NAT rules
data:    Load balancing rules
data:
info:    network lb frontend-ip set command OK
```

Paraméter beállításai:

```txt
-h, --help                                                         output usage information
-v, --verbose                                                      use verbose output
--json                                                             use json output
-g, --resource-group <resource-group>                              the name of the resource group
-l, --lb-name <lb-name>                                            the name of the load balancer
-n, --name <name>                                                  the name of the frontend ip configuration
-a, --private-ip-address <private-ip-address>                      the private ip address
-o, --private-ip-allocation-method <private-ip-allocation-method>  the private ip allocation method [Static, Dynamic]
-u, --public-ip-id <public-ip-id>                                  the public ip identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
-i, --public-ip-name <public-ip-name>                              the public ip name.
This public ip must exist in the same resource group as the lb.
Please use public-ip-id if that is not the case.
-b, --subnet-id <subnet-id>                                        the subnet id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/VirtualNetworks/<vnet-name>/subnets/<subnet-name>
-e, --subnet-name <subnet-name>                                    the subnet name
-m, --vnet-name <vnet-name>                                        the virtual network name.
This virtual network must exist in the same resource group as the lb.
Please use subnet-id if that is not the case.
-s, --subscription <subscription>                                  the subscription identifier
```

<BR>

```azurecli
network lb frontend-ip list [options] <resource-group> <lb-name>
```

Felsorolja a terheléselosztó számára konfigurált összes előtér-IP-erőforrást.

```azurecli
azure network lb frontend-ip list -g myresourcegroup -l mylb

info:    Executing command network lb frontend-ip list
+ Looking up the load balancer "mylb"
data:    Name         Provisioning state  Private IP allocation method  Subnet
data:    -----------  ------------------  ----------------------------  ------
data:    myprivateip  Succeeded           Dynamic
info:    network lb frontend-ip list command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
```
A Load Balancerhez társított előtéri IP-objektum törlése

```azurecli
network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
info:    Executing command network lb frontend-ip delete
+ Looking up the load balancer "mylb"
Delete frontend ip configuration "myfrontendip"? [y/n] y
+ Updating load balancer "mylb"
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the frontend ip configuration
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**A terheléselosztó háttérbeli címkészlet kezelésére szolgáló parancsok**

```azurecli
network lb address-pool create [options] <resource-group> <lb-name> <name>
```

Hozzon létre egy háttér-címkészletet a terheléselosztó számára.

```azurecli
azure network lb address-pool create -g myresourcegroup --lb-name mylb -n myaddresspool

info:    Executing command network lb address-pool create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourgroup/providers/Microso.Network/loadBalancers/mylb/backendAddressPools/myaddresspool
data:    Name:                      myaddresspool
data:    Type:                      Microsoft.Network/loadBalancers/backendAddressPools
data:    Provisioning state:        Succeeded
data:    Backend IP configurations:
data:    Load balancing rules:
data:
info:    network lb address-pool create command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the backend address pool
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb address-pool list [options] <resource-group> <lb-name>
```

Egy adott erőforráscsoport háttérbeli IP-címkészlet-tartományának listázása

```azurecli
azure network lb address-pool list -g myresourcegroup -l mylb

info:    Executing command network lb address-pool list
+ Looking up the load balancer "mylb"
data:    Name           Provisioning state
data:    -------------  ------------------
data:    mybackendpool  Succeeded
info:    network lb address-pool list command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network lb address-pool delete [options] <resource-group> <lb-name> <name>
```

Eltávolítja a háttérbeli IP-készlet tartományának erőforrását a Load balancerből.

```azurecli
azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

info:    Executing command network lb address-pool delete
+ Looking up the load balancer "mylb"
Delete backend address pool "mybackendpool"? [y/n] y
+ Updating load balancer "mylb"
info:    network lb address-pool delete command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the backend address pool
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**A terheléselosztó szabályainak kezelésére szolgáló parancsok**

```azurecli
network lb rule create [options] <resource-group> <lb-name> <name>
```
Hozzon létre terheléselosztó-szabályokat.

Létrehozhat egy terheléselosztó-szabályt, amely a Load Balancer és a háttérbeli címkészlet végpontját konfigurálja a bejövő hálózati forgalom fogadásához. A beállítások a háttérbeli címkészlet tartományához tartozó előtérbeli IP-végpontok és portok portjait is tartalmazzák.

Az alábbi példa bemutatja, hogyan hozhat létre egy terheléselosztó-szabályt, az előtér-végpont a 80-as TCP-portra és a 8080-es portra küldött terheléselosztási hálózati forgalmat a háttérbeli címkészlet tartományához.

```azurecli
azure network lb rule create -g myresourcegroup -l mylb -n mylbrule -p tcp -f 80 -b 8080 -i 10


info:    Executing command network lb rule create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Loading rule state
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mylbrule
data:    Name:                      mylbrule
data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
data:    Provisioning state:        Succeeded
data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
data:    Protocol:                  Tcp
data:    Frontend port:             80
data:    Backend port:              8080
data:    Enable floating IP:        false
data:    Idle timeout in minutes:   10
data:    Probes
data:
info:    network lb rule create command OK
```

<BR>

```azurecli
network lb rule set [options] <resource-group> <lb-name> <name>
```

Egy meglévő terheléselosztó-szabályt frissít egy adott erőforráscsoporthoz. A következő példában a szabály nevét módosítottuk a mylbrule és a mynewlbrule között.

```azurecli
azure network lb rule set -g myresourcegroup -l mylb -n mylbrule -r mynewlbrule -p tcp -f 80 -b 8080 -i 10 -t myfrontendip -o mybackendpool

info:    Executing command network lb rule set
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Loading rule state
data:    Id:                        /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/loadBalancingRules/mynewlbrule
data:    Name:                      mynewlbrule
data:    Type:                      Microsoft.Network/loadBalancers/loadBalancingRules
data:    Provisioning state:        Succeeded
data:    Frontend IP configuration: /subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend address pool:      id=/subscriptions/###############################/resourceGroups/yresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
data:    Protocol:                  Tcp
data:    Frontend port:             80
data:    Backend port:              8080
data:    Enable floating IP:        false
data:    Idle timeout in minutes:   10
data:    Probes
data:
info:    network lb rule set command OK
```

Paraméter beállításai:

```txt
-h, --help                                         output usage information
-v, --verbose                                      use verbose output
--json                                             use json output
-g, --resource-group <resource-group>              the name of the resource group
-l, --lb-name <lb-name>                            the name of the load balancer
-n, --name <name>                                  the name of the rule
-r, --new-rule-name <new-rule-name>                new rule name
-p, --protocol <protocol>                          the rule protocol
-f, --frontend-port <frontend-port>                the frontend port
-b, --backend-port <backend-port>                  the backend port
-e, --enable-floating-ip <enable-floating-ip>      enable floating point ip
-i, --idle-timeout <idle-timeout>                  the idle timeout in minutes
-a, --probe-name [probe-name]                      the name of the probe defined in the same load balancer
-t, --frontend-ip-name <frontend-ip-name>          the name of the frontend ip configuration in the same load balancer
-o, --backend-address-pool <backend-address-pool>  name of the backend address pool defined in the same load balancer
-s, --subscription <subscription>                  the subscription identifier
```


```azurecli
network lb rule list [options] <resource-group> <lb-name>
```

Felsorolja az adott erőforráscsoport terheléselosztó számára konfigurált terheléselosztó-szabályokat.

```azurecli
azure network lb rule list -g myresourcegroup -l mylb

info:    Executing command network lb rule list
+ Looking up the load balancer "mylb"
data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
info:    network lb rule list command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```

```azurecli
network lb rule delete [options] <resource-group> <lb-name> <name>
```

Töröl egy terheléselosztó-szabályt.

```azurecli
azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

info:    Executing command network lb rule delete
+ Looking up the load balancer "mylb"
Delete load balancing rule mynewlbrule? [y/n] y
+ Updating load balancer "mylb"
info:    network lb rule delete command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the rule
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**A terheléselosztó bejövő NAT-szabályainak kezeléséhez szükséges parancsok**

```azurecli
network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
```
Létrehoz egy bejövő NAT-szabályt a terheléselosztó számára.

A következő példában létrehozunk egy NAT-szabályt az előtér-IP-címről (amely korábban az "Azure Network frontend-IP" paranccsal lett definiálva) egy bejövő figyelő porttal és kimenő porttal, amelyet a terheléselosztó a hálózati forgalom elküldéséhez használ.

```azurecli
azure network lb inbound-nat-rule create -g myresourcegroup -l mylb -n myinboundnat -p tcp -f 80 -b 8080 -i myfrontendip

info:    Executing command network lb inbound-nat-rule create
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
data:    Name:                      myinboundnat
data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
data:    Provisioning state:        Succeeded
data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend IP configuration
data:    Protocol                   Tcp
data:    Frontend port              80
data:    Backend port               8080
data:    Enable floating IP         false
info:    network lb inbound-nat-rule create command OK
```

Paraméter beállításai:

```txt
-h, --help                                     output usage information
-v, --verbose                                  use verbose output
--json                                         use json output
-g, --resource-group <resource-group>          the name of the resource group
-l, --lb-name <lb-name>                        the name of the load balancer
-n, --name <name>                              the name of the inbound NAT rule
-p, --protocol <protocol>                      the rule protocol [tcp,udp]
-f, --frontend-port <frontend-port>            the frontend port [0-65535]
-b, --backend-port <backend-port>              the backend port [0-65535]
-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
-m, --vm-id <vm-id>                            the VM id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
-a, --vm-name <vm-name>                        the VM name.This VM must exist in the same resource group as the lb.
Please use vm-id if that is not the case.
this parameter will be ignored if --vm-id is specified
-s, --subscription <subscription>              the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
```
Frissíti egy meglévő bejövő NAT-szabályt. A következő példában a 80 és 81 közötti bejövő figyelési portot módosítottuk.

```azurecli
azure network lb inbound-nat-rule set -g group-1 -l mylb -n myinboundnat -p tcp -f 81 -b 8080 -i myfrontendip

info:    Executing command network lb inbound-nat-rule set
+ Looking up the load balancer "mylb"
+ Updating load balancer "mylb"
+ Looking up the load balancer "mylb"
data:    Id:                        /subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/inboundNatRules/myinboundnat
data:    Name:                      myinboundnat
data:    Type:                      Microsoft.Network/loadBalancers/inboundNatRules
data:    Provisioning state:        Succeeded
data:    Frontend IP Configuration: id=/subscriptions/###############################/resourceGroups/group-1/providers/Microsoft.Network/loadBalancers/mylb/frontendIPConfigurations/myfrontendip
data:    Backend IP configuration
data:    Protocol                   Tcp
data:    Frontend port              81
data:    Backend port               8080
data:    Enable floating IP         false
info:    network lb inbound-nat-rule set command OK
```

Paraméter beállításai:

```txt
-h, --help                                     output usage information
-v, --verbose                                  use verbose output
--json                                         use json output
-g, --resource-group <resource-group>          the name of the resource group
-l, --lb-name <lb-name>                        the name of the load balancer
-n, --name <name>                              the name of the inbound NAT rule
-p, --protocol <protocol>                      the rule protocol [tcp,udp]
-f, --frontend-port <frontend-port>            the frontend port [0-65535]
-b, --backend-port <backend-port>              the backend port [0-65535]
-e, --enable-floating-ip <enable-floating-ip>  enable floating point ip [true,false]
-i, --frontend-ip <frontend-ip>                the name of the frontend ip configuration
-m, --vm-id [vm-id]                            the VM id.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/<vm-name>
-a, --vm-name <vm-name>                        the VM name.
This virtual machine must exist in the same resource group as the lb.
Please use vm-id if that is not the case
-s, --subscription <subscription>              the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule list [options] <resource-group> <lb-name>
```

Felsorolja a Load Balancer összes bejövő NAT-szabályát.

```azurecli
azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

info:    Executing command network lb inbound-nat-rule list
+ Looking up the load balancer "mylb"
data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
---------------------
data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

info:    network lb inbound-nat-rule list command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-s, --subscription <subscription>      the subscription identifier
```
<BR>

```azurecli
network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>
```

Törli a terheléselosztó NAT-szabályát egy adott erőforráscsoport esetében.

```azurecli
azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

info:    Executing command network lb inbound-nat-rule delete
+ Looking up the load balancer "mylb"
Delete inbound NAT rule "myinboundnat?" [y/n] y
+ Updating load balancer "mylb"
info:    network lb inbound-nat-rule delete command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-l, --lb-name <lb-name>                the name of the load balancer
-n, --name <name>                      the name of the inbound NAT rule
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```

**A nyilvános IP-címek kezelésére szolgáló parancsok**

```azurecli
network public-ip create [options] <resource-group> <name> <location>
```
Létrehoz egy nyilvános IP-erőforrást. Létrehoz egy nyilvános IP-erőforrást, és hozzárendeli a tartománynevet.

```azurecli
azure network public-ip create -g myresourcegroup -n mytestpublicip1 -l eastus -d azureclitest -a "Dynamic"
info:    Executing command network public-ip create
+ Looking up the public ip "mytestpublicip1"
+ Creating public ip address "mytestpublicip1"
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
data:    Name:                 mytestpublicip1
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Dynamic
data:    Idle timeout:         4
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip create command OK
```


Paraméter beállításai:

```txt
-h, --help                                   output usage information
-v, --verbose                                use verbose output
--json                                       use json output
-g, --resource-group <resource-group>        the name of the resource group
-n, --name <name>                            the name of the public ip
-l, --location <location>                    the location
-d, --domain-name-label <domain-name-label>  the domain name label.
This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
-i, --idletimeout <idletimeout>              the idle timeout in minutes
-f, --reverse-fqdn <reverse-fqdn>            the reverse fqdn
-t, --tags <tags>                            the list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>            the subscription identifier
```
<br>

```azurecli
network public-ip set [options] <resource-group> <name>
```
Egy meglévő nyilvános IP-erőforrás tulajdonságainak frissítése. A következő példában a nyilvános IP-címet Dinamikusról statikusra módosították.

```azurecli
azure network public-ip set -g group-1 -n mytestpublicip1 -d azureclitest -a "Static"
info:    Executing command network public-ip set
+ Looking up the public ip "mytestpublicip1"
+ Updating public ip address "mytestpublicip1"
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip1
data:    Name:                 mytestpublicip1
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Static
data:    Idle timeout:         4
data:    IP Address:           (static IP address)
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip set command OK
```

Paraméter beállításai:

```txt
-h, --help                                   output usage information
-v, --verbose                                use verbose output
--json                                       use json output
-g, --resource-group <resource-group>        the name of the resource group
-n, --name <name>                            the name of the public ip
-d, --domain-name-label [domain-name-label]  the domain name label.
This set DNS to <domain-name-label>.<location>.cloudapp.azure.com
-a, --allocation-method <allocation-method>  the allocation method [Static][Dynamic]
-i, --idletimeout <idletimeout>              the idle timeout in minutes
-f, --reverse-fqdn [reverse-fqdn]            the reverse fqdn
-t, --tags <tags>                            the list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
--no-tags                                    remove all existing tags
-s, --subscription <subscription>            the subscription identifier
```

<br>

```azurecli
network public-ip list [options] <resource-group>
```
Az erőforráscsoporthoz tartozó összes nyilvános IP-erőforrás felsorolása.

```azurecli
azure network public-ip list -g myresourcegroup

info:    Executing command network public-ip list
+ Getting the public ip addresses
data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-s, --subscription <subscription>      the subscription identifier
```

<BR>

```azurecli
network public-ip show [options] <resource-group> <name>
```

Egy erőforráscsoport nyilvános IP-erőforrásának nyilvános IP-tulajdonságait jeleníti meg.

```azurecli
azure network public-ip show -g myresourcegroup -n mytestpublicip

info:    Executing command network public-ip show
+ Looking up the public ip "mytestpublicip1"
data:    Id:                   /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mytestpublicip
data:    Name:                 mytestpublicip
data:    Type:                 Microsoft.Network/publicIPAddresses
data:    Location:             eastus
data:    Provisioning state:   Succeeded
data:    Allocation method:    Static
data:    Idle timeout:         4
data:    IP Address:           (static IP address)
data:    Domain name label:    azureclitest
data:    FQDN:                 azureclitest.eastus.cloudapp.azure.com
info:    network public-ip show command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the public IP
-s, --subscription <subscription>      the subscription identifier
```


```azurecli
network public-ip delete [options] <resource-group> <name>
```

Törli a nyilvános IP-erőforrást.

```azurecli
azure network public-ip delete -g group-1 -n mypublicipname
info:    Executing command network public-ip delete
+ Looking up the public ip "mypublicipname"
Delete public ip address "mypublicipname"? [y/n] y
+ Deleting public ip address "mypublicipname"
info:    network public-ip delete command OK
```

Paraméter beállításai:

```txt
-h, --help                             output usage information
-v, --verbose                          use verbose output
--json                                 use json output
-g, --resource-group <resource-group>  the name of the resource group
-n, --name <name>                      the name of the public IP
-q, --quiet                            quiet mode, do not ask for delete confirmation
-s, --subscription <subscription>      the subscription identifier
```


**A hálózati adapterek kezelésére szolgáló parancsok**

```azurecli
network nic create [options] <resource-group> <name> <location>
```
Létrehoz egy hálózati adapter (NIC) nevű erőforrást, amely terheléselosztáshoz vagy virtuális géphez való hozzárendeléshez használható.

```azurecli
azure network nic create -g myresourcegroup -l eastus -n testnic1 --subnet-name subnet-1 --subnet-vnet-name myvnet

info:    Executing command network nic create
+ Looking up the network interface "testnic1"
+ Looking up the subnet "subnet-1"
+ Creating network interface "testnic1"
+ Looking up the network interface "testnic1"
data:    Id:                     /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/networkInterfaces/testnic1
data:    Name:                   testnic1
data:    Type:                   Microsoft.Network/networkInterfaces
data:    Location:               eastus
data:    Provisioning state:     Succeeded
data:    IP configurations:
data:       Name:                         NIC-config
data:       Provisioning state:           Succeeded
data:       Private IP address:           10.0.0.5
data:       Private IP Allocation Method: Dynamic
data:       Subnet:                       /subscriptions/c4a17ddf-aa84-491c-b6f9-b90d882299f7/resourceGroups/group-1/providers/Microsoft.Network/virtualNetworks/myVNET/subnets/Subnet-1
```

Paraméter beállításai:

```txt
-h, --help                                                       output usage information
-v, --verbose                                                    use verbose output
--json                                                           use json output
-g, --resource-group <resource-group>                            the name of the resource group
-n, --name <name>                                                the name of the network interface
-l, --location <location>                                        the location
-w, --network-security-group-id <network-security-group-id>      the network security group identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/networkSecurityGroups/<nsg-name>
-o, --network-security-group-name <network-security-group-name>  the network security group name.
This network security group must exist in the same resource group as the nic.
Please use network-security-group-id if that is not the case.
-i, --public-ip-id <public-ip-id>                                the public IP identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/publicIPAddresses/<public-ip-name>
-p, --public-ip-name <public-ip-name>                            the public IP name.
This public ip must exist in the same resource group as the nic.
Please use public-ip-id if that is not the case.
-a, --private-ip-address <private-ip-address>                    the private IP address
-u, --subnet-id <subnet-id>                                      the subnet identifier.
e.g. /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>/subnets/<subnet-name>
--subnet-name <subnet-name>                                      the subnet name
-m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
-t, --tags <tags>                                                the comma separated list of tags.
Can be multiple. In the format of "name=value".
Name is required and value is optional.
For example, -t tag1=value1;tag2
-s, --subscription <subscription>                                the subscription identifier
data:
info:    network nic create command OK
```

<BR>

```azurecli
network nic set [options] <resource-group> <name>
network nic list [options] <resource-group>
network nic show [options] <resource-group> <name>
network nic delete [options] <resource-group> <name>
```

**A hálózati biztonsági csoportok kezelésére szolgáló parancsok**

```azurecli
network nsg create [options] <resource-group> <name> <location>
network nsg set [options] <resource-group> <name>
network nsg list [options] <resource-group>
network nsg show [options] <resource-group> <name>
network nsg delete [options] <resource-group> <name>
```

**A hálózati biztonsági csoport szabályainak kezeléséhez szükséges parancsok**

```azurecli
network nsg rule create [options] <resource-group> <nsg-name> <name>
network nsg rule set [options] <resource-group> <nsg-name> <name>
network nsg rule list [options] <resource-group> <nsg-name>
network nsg rule show [options] <resource-group> <nsg-name> <name>
network nsg rule delete [options] <resource-group> <nsg-name> <name>
```

**A Traffic Manager-profil kezelésére szolgáló parancsok**

```azurecli
network traffic-manager profile create [options] <resource-group> <name>
network traffic-manager profile set [options] <resource-group> <name>
network traffic-manager profile list [options] <resource-group>
network traffic-manager profile show [options] <resource-group> <name>
network traffic-manager profile delete [options] <resource-group> <name>
network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>
```

**A Traffic Manager-végpontok kezelésére szolgáló parancsok**

```azurecli
network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>
```

**A virtuális hálózati átjárók kezelésére szolgáló parancsok**

```azurecli
network gateway list [options] <resource-group>
```

## <a name="resource-provider-registrations"></a>Erőforrás-szolgáltatói regisztrációk
**Jelenleg regisztrált szolgáltatók listázása a Resource Managerben**

```azurecli
provider list [options]
```

**A kért szolgáltatói névtér részleteinek megjelenítése**

```azurecli
provider show [options] <namespace>
```

**Szolgáltató regisztrálása az előfizetéssel**

```azurecli
provider register [options] <namespace>
```

**Szolgáltató regisztrációjának törlése az előfizetéssel**

```azurecli
provider unregister [options] <namespace>
```

## <a name="resources"></a>Erőforrások
**Erőforrás-csoport létrehozása**

```azurecli
resource create [options] <resource-group> <name> <resource-type> <location> <api-version>
```

**Egy erőforráscsoport erőforrásának frissítése sablon vagy paraméterek nélkül**

```azurecli
resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>
```

**Az erőforrások listája**

```azurecli
resource list [options] [resource-group]
```

**Egy erőforráscsoport vagy előfizetés egy erőforrásának beolvasása**

```azurecli
resource show [options] <resource-group> <name> <resource-type> <api-version>
```

**Erőforrás törlése egy erőforráscsoporthoz**

```azurecli
resource delete [options] <resource-group> <name> <resource-type> <api-version>
```

## <a name="azure-roles"></a>Azure-szerepkörök
**Az összes elérhető szerepkör-definíció beolvasása**

```azurecli
role list [options]
```

**Elérhető szerepkör-definíció beszerzése**

```azurecli
role show [options] [name]
```

**A szerepkör-hozzárendelés kezelésére szolgáló parancsok**

```azurecli
role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
```

## <a name="storage-objects"></a>Tárolási objektumok
**A Storage-fiókok kezelésére szolgáló parancsok**

```azurecli
storage account list [options]
storage account show [options] <name>
storage account create [options] <name>
storage account set [options] <name>
storage account delete [options] <name>
```

**A Storage-fiók kulcsainak kezelésére szolgáló parancsok**

```azurecli
storage account keys list [options] <name>
storage account keys renew [options] <name>
```

**A tárolási kapcsolati karakterlánc megjelenítésére szolgáló parancsok**

```azurecli
storage account connectionstring show [options] <name>
```

**A tároló-tárolók kezelésére szolgáló parancsok**

```azurecli
storage container list [options] [prefix]
storage container show [options] [container]
storage container create [options] [container]
storage container delete [options] [container]
storage container set [options] [container]
```

**A Storage-tároló közös hozzáférésű aláírásait kezelő parancsok**

```azurecli
storage container sas create [options] [container] [permissions] [expiry]
```

**A Storage-tároló tárolt hozzáférési házirendjeinek kezelésére szolgáló parancsok**

```azurecli
storage container policy create [options] [container] [name]
storage container policy show [options] [container] [name]
storage container policy list [options] [container]
storage container policy set [options] [container] [name]
storage container policy delete [options] [container] [name]
```

**A tárolási Blobok kezelésére szolgáló parancsok**

```azurecli
storage blob list [options] [container] [prefix]
storage blob show [options] [container] [blob]
storage blob delete [options] [container] [blob]
storage blob upload [options] [file] [container] [blob]
storage blob download [options] [container] [blob] [destination]
```

**A blob-másolási műveletek kezelésére szolgáló parancsok**

```azurecli
storage blob copy start [options] [sourceUri] [destContainer]
storage blob copy show [options] [container] [blob]
storage blob copy stop [options] [container] [blob] [copyid]
```

**A tárolási blob közös hozzáférésű aláírását kezelő parancsok**

```azurecli
storage blob sas create [options] [container] [blob] [permissions] [expiry]
```

**A tárolási fájlmegosztás kezelésére szolgáló parancsok**

```azurecli
storage share create [options] [share]
storage share show [options] [share]
storage share delete [options] [share]
storage share list [options] [prefix]
```

**A tárolási fájlok kezelésére szolgáló parancsok**

```azurecli
storage file list [options] [share] [path]
storage file delete [options] [share] [path]
storage file upload [options] [source] [share] [path]
storage file download [options] [share] [path] [destination]
```

**A tárolási fájlok könyvtárának kezelésére szolgáló parancsok**

```azurecli
storage directory create [options] [share] [path]
storage directory delete [options] [share] [path]
```

**A tárolási várólisták kezelésére szolgáló parancsok**

```azurecli
storage queue create [options] [queue]
storage queue list [options] [prefix]
storage queue show [options] [queue]
storage queue delete [options] [queue]
```

**A tárolási üzenetsor közös hozzáférésű aláírásait kezelő parancsok**

```azurecli
storage queue sas create [options] [queue] [permissions] [expiry]
```

**A tárolási üzenetsor tárolt hozzáférési házirendjeinek kezelésére szolgáló parancsok**

```azurecli
storage queue policy create [options] [queue] [name]
storage queue policy show [options] [queue] [name]
storage queue policy list [options] [queue]
storage queue policy set [options] [queue] [name]
storage queue policy delete [options] [queue] [name]
```

**A tárolási naplózási tulajdonságok kezelésére szolgáló parancsok**

```azurecli
storage logging show [options]
storage logging set [options]
```

**Parancsok a tárolási metrikák tulajdonságainak kezeléséhez**

```azurecli
storage metrics show [options]
storage metrics set [options]
```

**A tárolási táblák kezelésére szolgáló parancsok**

```azurecli
storage table create [options] [table]
storage table list [options] [prefix]
storage table show [options] [table]
storage table delete [options] [table]
```

**A Storage-tábla közös hozzáférésű aláírásait kezelő parancsok**

```azurecli
storage table sas create [options] [table] [permissions] [expiry]
```

**A Storage-tábla tárolt hozzáférési házirendjeinek kezelésére szolgáló parancsok**

```azurecli
storage table policy create [options] [table] [name]
storage table policy show [options] [table] [name]
storage table policy list [options] [table]
storage table policy set [options] [table] [name]
storage table policy delete [options] [table] [name]
```

## <a name="tags"></a>Címkék
**Címke hozzáadása**

```azurecli
tag create [options] <name> <value>
```

**Teljes címke vagy címke értékének eltávolítása**

```azurecli
tag delete [options] <name> <value>
```

**A címke információinak felsorolása**

```azurecli
tag list [options]
```

**Címke beolvasása**

```azurecli
tag show [options] [name]
```

## <a name="virtual-machines"></a>Virtual Machines
**Virtuális gép létrehozása**

```azurecli
vm create [options] <resource-group> <name> <location> <os-type>
```

**Alapértelmezett erőforrásokkal rendelkező virtuális gép létrehozása**

```azurecli
vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password
```

> [!TIP]
> A CLI 0,10-es verziójától kezdve egy rövid aliast is megadhat, például a "UbuntuLTS" vagy a "Win2012R2Datacenter" nevet, `image-urn` amely néhány népszerű Piactéri rendszerképet tartalmaz. Futtatás `azure help vm quick-create` a beállításokhoz. Emellett a 0,10-es verziótól kezdődően `azure vm quick-create` a Premium Storage alapértelmezés szerint a kiválasztott régióban érhető el.
> 
> 

**Fiókon belüli virtuális gépek listázása**

```azurecli
vm list [options]
```

**Egy erőforráscsoport-beli virtuális gép beolvasása**

```azurecli
vm show [options] <resource-group> <name>
```

**Egy erőforráscsoport-beli virtuális gép törlése**

```azurecli
vm delete [options] <resource-group> <name>
```

**Egy virtuális gép leállítása egy erőforráscsoport belül**

```azurecli
vm stop [options] <resource-group> <name>
```

**Egy erőforráscsoport egy virtuális gép újraindítása**

```azurecli
vm restart [options] <resource-group> <name>
```

**Egy virtuális gép elindítása egy erőforráscsoport belül**

```azurecli
vm start [options] <resource-group> <name>
```

**Egy adott erőforráscsoport egy virtuális gép leállítása és a számítási erőforrások felszabadítása**

```azurecli
vm deallocate [options] <resource-group> <name>
```

**Elérhető virtuálisgép-méretek listázása**

```azurecli
vm sizes [options]
```

**A virtuális gép rögzítése operációsrendszer-lemezképként vagy virtuálisgép-rendszerképként**

```azurecli
vm capture [options] <resource-group> <name> <vhd-name-prefix>
```

**A virtuális gép állapotának beállítása általánosított értékre**

```azurecli
vm generalize [options] <resource-group> <name>
```

**A virtuális gép példány nézetének beolvasása**

```azurecli
vm get-instance-view [options] <resource-group> <name>
```

**Lehetővé teszi a virtuális gép Távoli asztal hozzáférési vagy SSH-beállításainak alaphelyzetbe állítását, valamint a rendszergazda vagy a sudo-hatóság által felügyelt fiók jelszavának alaphelyzetbe állítását.**

```azurecli
vm reset-access [options] <resource-group> <name>
```

**Virtuális gép frissítése új adattal**

```azurecli
vm set [options] <resource-group> <name>
```

**A virtuális gép adatlemezeit kezelő parancsok**

```azurecli
vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
vm disk detach [options] <resource-group> <vm-name> <lun>
vm disk attach [options] <resource-group> <vm-name> [vhd-url]
```

**A virtuális gépek erőforrás-bővítményeinek kezelésére szolgáló parancsok**

```azurecli
vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
vm extension get [options] <resource-group> <vm-name>
```

**A Docker-virtuális gép kezelésére szolgáló parancsok**

```azurecli
vm docker create [options] <resource-group> <name> <location> <os-type>
```

**A virtuálisgép-rendszerképek kezelésére szolgáló parancsok**

```azurecli
vm image list-publishers [options] <location>
vm image list-offers [options] <location> <publisher>
vm image list-skus [options] <location> <publisher> <offer>
vm image list [options] <location> <publisher> [offer] [sku]
```
