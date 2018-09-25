---
title: Az Azure CLI-parancsok a Resource Manager módban |} A Microsoft Docs
description: Az Azure parancssori felület (CLI) parancsai a Resource Manager-alapú üzemi modellben-erőforrások kezeléséhez
services: virtual-machines-linux,virtual-machines-windows,virtual-network,mobile-services,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: be37da5b-72fe-41a1-9fa0-8937b69464ec
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: command-line-interface
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: danlep
ms.openlocfilehash: 5aecb218b66c06c87446e35e5c7c9ca8108b956c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995550"
---
# <a name="azure-cli-commands-in-resource-manager-mode"></a>Resource Manager módban az Azure CLI-parancsok
A cikk ismerteti a szintaxist és a beállítások létrehozása és kezelése az Azure-erőforrások Azure Resource Manager-alapú üzemi modellben használata általában az Azure parancssori felület (CLI) parancsok. Ezeket a parancsokat a parancssori felület futtatásával Resource Manager (arm) módban érhető el. Ez a nem teljes, és a parancssori felület verziójának valószínűleg eltérő parancsok és paraméterek. Azure-erőforrások és -erőforráscsoportok általános áttekintéséért lásd: [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).  

> [!NOTE]
> Ez a cikk Resource Manager módban parancsok láthatók az Azure CLI-ben más néven az Azure klasszikus parancssori felület. A Resource Manager-modell használatát is kipróbálhatja a [Azure CLI-vel](/cli/azure/install-az-cli2), a következő generációs többplatformos parancssori felület.
>Tudjon meg többet a [régi és az új Azure-felületekre](/cli/azure/old-and-new-clis).
>

A kezdéshez először [az Azure CLI telepítése](../cli-install-nodejs.md) és [csatlakozhat az Azure-előfizetés](/cli/azure/authenticate-azure-cli).

Aktuális parancs szintaxisát és a beállítások a Resource Manager módban a parancssorból, írja be a következőt `azure help` vagy egy adott parancs súgójának megjelenítéséhez `azure help [command]`. Hozhat létre és kezelhet konkrét Azure-szolgáltatás is található CLI-példák a dokumentációt.

Választható paraméterek: szögletes zárójelben láthatók (például `[parameter]`). Az összes többi paraméter szükség.

A parancs-specifikus választható paraméterek: itt dokumentált kívül három nem kötelező paraméter, például kérésbeállításokat és állapotkódok részletes kimenet megjelenítéséhez használható. A `-v` paraméter biztosít részletes kimenet, és a `-vv` paramétert tartalmaz még részletesebb részletes kimenet. A `--json` beállítás eredménye nyers json formátumban adja vissza.

## <a name="setting-the-resource-manager-mode"></a>Az erőforrás-kezelő mód beállítása
A következő paranccsal engedélyezze az Azure CLI Resource Manager módban parancsokat.

    azure config mode arm

> [!NOTE]
> A CLI Azure Resource Manager módban és Azure szolgáltatásfelügyelet módban kölcsönösen kizárják egymást. Egy módban létrehozott erőforrásokat, a más mód nem kezelhető.
> 
> 

## <a name="azure-account-manage-your-account-information"></a>Azure-fiók: a fiók adatainak kezelése
Az Azure-előfizetése adatait használják az eszközt a fiókhoz való csatlakozáshoz.

**Az importált előfizetések listája**

    account list [options]

**Előfizetés részleteinek megjelenítéséhez**  

    account show [options] [subscriptionNameOrId]

**Állítsa be az aktuális előfizetésben**

    account set [options] <subscriptionNameOrId>

**Távolítsa el, egy előfizetést, vagy a környezetben, vagy törölje az összes fiókot és környezetet a tárolt adatok**  

    account clear [options]

**A fiók környezet kezelését végző parancsokról**  

    account env list [options]
    account env show [options] [environment]
    account env add [options] [environment]
    account env set [options] [environment]
    account env delete [options] [environment]

## <a name="azure-ad-commands-to-display-active-directory-objects"></a>Azure ad-ben: az Active Directory-objektumok megjelenítéséhez parancsok
**Active directory-alkalmazások megjelenítésére szolgáló parancsok**

    ad app create [options]
    ad app delete [options] <object-id>

**Az active directory-csoportok megjelenítéséhez parancsok**

    ad group list [options]
    ad group show [options]

**Parancsok egy active directory-sub csoport vagy a tag adatainak megadása**

    ad group member list [options] [objectId]

**Az active directory egyszerű szolgáltatások megjelenítéséhez parancsok**

    ad sp list [options]
    ad sp show [options]
    ad sp create [options] <application-id>
    ad sp delete [options] <object-id>

**Parancsok active directory-felhasználók megjelenítése**

    ad user list [options]
    ad user show [options]

## <a name="azure-availset-commands-to-manage-your-availability-sets"></a>az Azure availset: parancsokat a rendelkezésre állási készletek felügyelete
**Létrehoz egy rendelkezésre állási csoportot az erőforráscsoporton belül**

    availset create [options] <resource-group> <name> <location> [tags]

**Felsorolja a rendelkezésre állási csoportok egy erőforráscsoporton belül**

    availset list [options] <resource-group>

**Lekérdezi a rendelkezésre állási csoporthoz az erőforráscsoporton belül**

    availset show [options] <resource-group> <name>

**Törli a rendelkezésre állási csoporthoz az erőforráscsoporton belül**

    availset delete [options] <resource-group> <name>

## <a name="azure-config-commands-to-manage-your-local-settings"></a>az Azure-konfiguráció: a helyi beállításokat kezelését végző parancsokról
**Azure CLI konfigurációs beállítások**

    config list [options]

**Egy konfigurációs beállítás törlése**

    config delete [options] <name>

**Egy konfigurációs beállítás frissítése**

    config set <name> <value>

**Az Azure CLI-vel működő mód beállítása vagy `arm` vagy `asm`**

    config mode [options] <modename>


## <a name="azure-feature-commands-to-manage-account-features"></a>az Azure-funkció: fiók szolgáltatások kezelésére szolgáló parancsokat
**Az előfizetéshez tartozó összes funkcióját listázása**

    feature list [options]

**Megjeleníti egy szolgáltatás**

    feature show [options] <providerName> <featureName>

**A hozzászólásláncnak funkció az erőforrás-szolgáltató regisztrálása**

    feature register [options] <providerName> <featureName>

## <a name="azure-group-commands-to-manage-your-resource-groups"></a>az Azure-csoportot: az erőforráscsoportok kezelésére szolgáló parancsokat
**Létrehoz egy erőforráscsoportot**

    group create [options] <name> <location>

**Egy erőforráscsoportba címkék beállítása**

    group set [options] <name> <tags>

**Töröl egy erőforráscsoportot**

    group delete [options] <name>

**Az előfizetéshez tartozó erőforráscsoportok listája**

    group list [options]

**Az előfizetés egy erőforráscsoport látható**

    group show [options] <name>

**Erőforrás-csoportok naplóinak kezelésére szolgáló parancsok**

    group log show [options] [name]

**Az üzemelő példány egy erőforráscsoportba tartozó kezelését végző parancsokról**

    group deployment create [options] [resource-group] [name]
    group deployment list [options] <resource-group> [state]
    group deployment show [options] <resource-group> [deployment-name]
    group deployment stop [options] <resource-group> [deployment-name]

**A helyi vagy a katalógus erőforráscsoport sablonjának kezelését végző parancsokról**

    group template list [options]
    group template show [options] <name>
    group template download [options] [name] [file]
    group template validate [options] <resource-group>

## <a name="azure-hdinsight-commands-to-manage-your-hdinsight-clusters"></a>az Azure hdinsight: a HDInsight-fürtök kezelését végző parancsokról
**Parancsok létrehozásához vagy egy fürt konfigurációs fájl hozzáadása**

    hdinsight config create [options] <configFilePath> <overwrite>
    hdinsight config add-config-values [options] <configFilePath>
    hdinsight config add-script-action [options] <configFilePath>

Példa: Hozzon létre egy fürtöt hoz létre futtatandó szkriptműveletet tartalmazó konfigurációs fájlt.

    hdinsight config create "C:\myFiles\configFile.config"
    hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

**Parancsot egy erőforráscsoport-fürt létrehozása**

    hdinsight cluster create [options] <clusterName>

Példa: A Storm létrehozása Linux-fürtön

    azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Storm --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 myNewCluster01

    info:    Executing command hdinsight cluster create
    + Submitting the request to create cluster...
    info:    hdinsight cluster create command OK

Példa: Egy parancsfájlművelet-fürt létrehozása

    azure hdinsight cluster create -g myarmgroup -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01

    info:    Executing command hdinsight cluster create
    + Submitting the request to create cluster...
    info:    hdinsight cluster create command OK

A paraméterbeállításokkal:

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


**Parancsot a fürt törlése**

    hdinsight cluster delete [options] <clusterName>

**Parancsot a fürt részleteinek megjelenítése**

    hdinsight cluster show [options] <clusterName>

**Paranccsal listázhatja az összes fürt (az egy adott erőforráscsoportban, ha meg van adva)**

    hdinsight cluster list [options]

**A parancs a fürt átméretezése**

    hdinsight cluster resize [options] <clusterName> <targetInstanceCount>

**A parancs egy fürt HTTP-hozzáférés engedélyezése**

    hdinsight cluster enable-http-access [options] <clusterName> <userName> <password>

**A parancs-fürthöz tartozó HTTP-hozzáférés letiltása**

    hdinsight cluster disable-http-access [options] <clusterName>

**Fürt RDP-hozzáférés engedélyezésére szolgáló parancs**

    hdinsight cluster enable-rdp-access [options] <clusterName> <rdpUserName> <rdpPassword> <rdpExpiryDate>

**A parancs-fürthöz tartozó HTTP-hozzáférés letiltása**

    hdinsight cluster disable-rdp-access [options] <clusterName>

## <a name="azure-insights-commands-related-to-monitoring-insights-events-alert-rules-autoscale-settings-metrics"></a>Azure insights: Insights (események, riasztási szabályok, az automatikus méretezési beállítások, metrikák) monitoring szolgáltatással kapcsolatos parancsok
**Az előfizetés, a korrelációs azonosító, egy erőforráscsoport, erőforrás vagy erőforrás-szolgáltató műveletnaplók beolvasása**

    insights logs list [options]

## <a name="azure-location-commands-to-get-the-available-locations-for-all-resource-types"></a>az Azure-helyen: parancsokat minden erőforrástípus esetén az elérhető helyek beolvasása
**Az elérhető helyek listája**

    location list [options]

## <a name="azure-network-commands-to-manage-network-resources"></a>az Azure network: parancsokat a hálózati erőforrások kezelése
**Virtuális hálózatok kezelését végző parancsokról**

    network vnet create [options] <resource-group> <name> <location>
Virtuális hálózatot hoz létre. A következő példában létrehozunk egy esetében az USA nyugati régiójában myresourcegroup erőforráscsoport newvnet nevű virtuális hálózatot.

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


A paraméterbeállításokkal:

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
<BR>

    network vnet set [options] <resource-group> <name>

Frissíti egy virtuális hálózati konfiguráció egy erőforráscsoporton belül.

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

A paraméterbeállításokkal:

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
<BR>

    network vnet list [options] <resource-group>

A parancs felsorolja egy erőforráscsoportba tartozó összes virtuális hálózatot.

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

A paraméterbeállításokkal:

      -h, --help                             output usage information
      -v, --verbose                          use verbose output
      --json                                 use json output
      -g, --resource-group <resource-group>  the name of the resource group
      -s, --subscription <subscription>      the subscription identifier

<BR>

    network vnet show [options] <resource-group> <name>
A parancs megjeleníti a virtuális hálózati tulajdonságok egy erőforráscsoportban.

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
<BR>

    network vnet delete [options] <resource-group> <name>
A parancs eltávolítja a virtuális hálózat.

    azure network vnet delete myresourcegroup newvnetX

    info:    Executing command network vnet delete
    + Looking up virtual network "newvnetX"
    Delete virtual network newvnetX? [y/n] y
    + Deleting virtual network "newvnetX"
    info:    network vnet delete command OK

A paraméterbeállításokkal:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the virtual network
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier


**Parancsok kezelheti a virtuális hálózati alhálózatokhoz**

    network vnet subnet create [options] <resource-group> <vnet-name> <name>

Egy másik alhálózatot ad hozzá egy meglévő virtuális hálózatot.

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

A paraméterbeállításokkal:

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

<BR>

    network vnet subnet set [options] <resource-group> <vnet-name> <name>

Beállítja egy adott virtuális hálózati alhálózat erőforráscsoporton belül.

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
<BR>

    network vnet subnet list [options] <resource-group> <vnet-name>

Egy adott virtuális hálózati erőforráscsoporton belül minden virtuális hálózat alhálózataiban sorolja fel.

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
<BR>

    network vnet subnet show [options] <resource-group> <vnet-name> <name>
Virtuális hálózat alhálózati tulajdonságokat jeleníti meg

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

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -e, --vnet-name <vnet-name>            the name of the virtual network
    -n, --name <name>                      the name of the subnet
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network vnet subnet delete [options] <resource-group> <vnet-name> <subnet-name>
Eltávolít egy alhálózatot egy meglévő virtuális hálózatot.

    azure network vnet subnet delete -g myresourcegroup --vnet-name newvnet -n subnet1

    info:    Executing command network vnet subnet delete
    + Looking up the subnet "subnet1"
    Delete subnet "subnet1"? [y/n] y
    + Deleting subnet "subnet1"
    info:    network vnet subnet delete command OK

A paraméterbeállításokkal:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -e, --vnet-name <vnet-name>            the name of the virtual network
     -n, --name <name>                      the subnet name
     -s, --subscription <subscription>      the subscription identifier
     -q, --quiet                            quiet mode, do not ask for delete confirmation

**Terheléselosztók kezelését végző parancsokról**

    network lb create [options] <resource-group> <name> <location>
Egy terheléselosztó készletet hoz létre.

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

A paraméterbeállításokkal:

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
<BR>

    network lb list [options] <resource-group>
Terheléselosztó erőforrásainak erőforráscsoporton belül sorolja fel.

    azure network lb list myresourcegroup

    info:    Executing command network lb list
    + Getting the load balancers
    data:    Name  Location
    data:    ----  --------
    data:    mylb  westus
    info:    network lb list command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb show [options] <resource-group> <name>

Megjeleníti az erőforráscsoporton belül egy adott terheléselosztó a terheléselosztó adatai betöltése

    azure network lb show myresourcegroup mylb -v

    info:    Executing command network lb show
    verbose: Looking up the load balancer "mylb"
    data:    Id:                           /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb
    data:    Name:                         mylb
    data:    Type:                         Microsoft.Network/loadBalancers
    data:    Location:                     westus
    data:    Provisioning state:           Succeeded
    info:    network lb show command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb delete [options] <resource-group> <name>

Load balancer-erőforrások törlése.

    azure network lb delete  myresourcegroup mylb

    info:    Executing command network lb delete
    + Looking up the load balancer "mylb"
    Delete load balancer "mylb"? [y/n] y
    + Deleting load balancer "mylb"
    info:    network lb delete command OK

A paraméterbeállításokkal:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -n, --name <name>                      the name of the load balancer
     -q, --quiet                            quiet mode, do not ask for delete confirmation
     -s, --subscription <subscription>      the subscription identifier

**A load Balancer vizsgálatok kezelését végző parancsokról**

    network lb probe create [options] <resource-group> <lb-name> <name>

Állapot-mintavételi konfiguráció létrehozása a terheléselosztóban. Ne feledje, a parancs futtatásához, a terheléselosztó előtérbeli ip-erőforrás (tekintse meg az "azure network frontend-ip" parancs egy IP-cím hozzárendelése a terheléselosztóhoz) van szükség.

    azure network lb probe create -g myresourcegroup --lb-name mylb -n mylbprobe --protocol tcp --port 80 -i 300

    info:    Executing command network lb probe create
    + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    info:    network lb probe create command OK

A paraméterbeállításokkal:

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

<BR>

    network lb probe set [options] <resource-group> <lb-name> <name>

Frissíti egy meglévő terheléselosztói mintavételező, új értékeket.

    azure network lb probe set -g myresourcegroup -l mylb -n mylbprobe -p mylbprobe1 -p TCP -o 443 -i 300

    info:    Executing command network lb probe set
        + Looking up the load balancer "mylb"
    + Updating load balancer "mylb"
    info:    network lb probe set command OK

A paraméter-beállítások

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
<BR>

    network lb probe list [options] <resource-group> <lb-name>

Egy terheléselosztó készletet: mintavétel tulajdonságainak listája.

    C:\>azure network lb probe list -g myresourcegroup -l mylb

    info:    Executing command network lb probe list
    + Looking up the load balancer "mylb"
    data:    Name       Protocol  Port  Path  Interval  Count
    data:    ---------  --------  ----  ----  --------  -----
    data:    mylbprobe  Tcp       443         300       2
    info:    network lb probe list command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier


    network lb probe delete [options] <resource-group> <lb-name> <name>
Eltávolítja a terheléselosztóhoz létrehozott mintavételt.

    azure network lb probe delete -g myresourcegroup -l mylb -n mylbprobe

    info:    Executing command network lb probe delete
    + Looking up the load balancer "mylb"
    Delete a probe "mylbprobe?" [y/n] y
    + Updating load balancer "mylb"
    info:    network lb probe delete command OK

**A terheléselosztó előtérbeli ip-konfigurációk kezelésére szolgáló parancsokat**

    network lb frontend-ip create [options] <resource-group> <lb-name> <name>
Egy előtérbeli IP-konfiguráció egy meglévő terheléselosztó készletet hoz létre.

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

<BR>

    network lb frontend-ip set [options] <resource-group> <lb-name> <name>

Frissíti egy meglévő konfigurációnak egy előtérbeli IP-cím. Az alábbi parancsot a meglévő terhelés a terheléselosztó előtérbeli IP-cím myfrontendip nevű mypubip5 nevű nyilvános IP-cím hozzáadása.

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

A paraméterbeállításokkal:

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

<BR>

    network lb frontend-ip list [options] <resource-group> <lb-name>

Felsorolja az összes előtérbeli IP-erőforrást a load Balancer konfigurálása.

    azure network lb frontend-ip list -g myresourcegroup -l mylb

    info:    Executing command network lb frontend-ip list
    + Looking up the load balancer "mylb"
    data:    Name         Provisioning state  Private IP allocation method  Subnet
    data:    -----------  ------------------  ----------------------------  ------
    data:    myprivateip  Succeeded           Dynamic
    info:    network lb frontend-ip list command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb frontend-ip delete [options] <resource-group> <lb-name> <name>
A terheléselosztóhoz társított előtérbeli IP-objektum törlése

    network lb frontend-ip delete -g myresourcegroup -l mylb -n myfrontendip
    info:    Executing command network lb frontend-ip delete
    + Looking up the load balancer "mylb"
    Delete frontend ip configuration "myfrontendip"? [y/n] y
    + Updating load balancer "mylb"

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the frontend ip configuration
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Egy terheléselosztó háttércímkészletek kezelését végző parancsokról**

    network lb address-pool create [options] <resource-group> <lb-name> <name>

Hozzon létre egy háttércímkészletet a terheléselosztó.

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

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -s, --subscription <subscription>      the subscription identifier

<BR>

    network lb address-pool list [options] <resource-group> <lb-name>

Egy adott erőforráscsoporthoz lista készlet háttérbeli IP-címtartomány

    azure network lb address-pool list -g myresourcegroup -l mylb

    info:    Executing command network lb address-pool list
    + Looking up the load balancer "mylb"
    data:    Name           Provisioning state
    data:    -------------  ------------------
    data:    mybackendpool  Succeeded
    info:    network lb address-pool list command OK

A paraméterbeállításokkal:

     -h, --help                             output usage information
     -v, --verbose                          use verbose output
     --json                                 use json output
     -g, --resource-group <resource-group>  the name of the resource group
     -l, --lb-name <lb-name>                the name of the load balancer
     -s, --subscription <subscription>      the subscription identifier

<BR>
    Network lb-címkészlet törlése [kapcsolók] < erőforráscsoport >< lb-neve > <name>

A háttérbeli IP-készlet tartomány erőforrás eltávolítja a terheléselosztóból.

    azure network lb address-pool delete -g myresourcegroup -l mylb -n mybackendpool

    info:    Executing command network lb address-pool delete
    + Looking up the load balancer "mylb"
    Delete backend address pool "mybackendpool"? [y/n] y
    + Updating load balancer "mylb"
    info:    network lb address-pool delete command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the backend address pool
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**A load balancer-szabályok kezelését végző parancsokról**

    network lb rule create [options] <resource-group> <lb-name> <name>
Load balancer-szabályok létrehozása.

Létrehozhat egy terheléselosztási szabály konfigurálása a load balancer és a háttérkiszolgáló-készlet címtartomány a bejövő hálózati forgalom fogadásához előtérbeli végpontját. Beállítások is, a portot az előtérbeli IP-cím végponthoz és a háttérkiszolgáló-készlet címtartománya portokat.

Az alábbi példa bemutatja, hogyan hozhat létre egy terheléselosztási szabályt, az előtérbeli végpont a 80 TCP-portot és a hálózati forgalom terheléselosztása háttérkiszolgáló-készlet címtartománya 8080-as porton küldi.

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

<BR>

    network lb rule set [options] <resource-group> <lb-name> <name>

Frissíti egy meglévő terheléselosztó-szabályt beállítani egy adott erőforráscsoportban. A következő példában hogy megváltozott a szabály nevét mylbrule mynewlbrule.

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

A paraméterbeállításokkal:

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


    network lb rule list [options] <resource-group> <lb-name>

Az összes terheléselosztási szabályok a terheléselosztó egy adott erőforráscsoportban konfigurálva.

    azure network lb rule list -g myresourcegroup -l mylb

    info:    Executing command network lb rule list
    + Looking up the load balancer "mylb"
    data:    Name         Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend address pool  Probe data

    data:    mynewlbrule  Succeeded           Tcp       80             8080          false               10                       /subscriptions/###############################/resourceGroups/myresourcegroup/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/mybackendpool
    info:    network lb rule list command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier

    network lb rule delete [options] <resource-group> <lb-name> <name>

Terheléselosztási szabály törlése.

    azure network lb rule delete -g myresourcegroup -l mylb -n mynewlbrule

    info:    Executing command network lb rule delete
    + Looking up the load balancer "mylb"
    Delete load balancing rule mynewlbrule? [y/n] y
    + Updating load balancer "mylb"
    info:    network lb rule delete command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the rule
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Parancsok kezelheti a terheléselosztó bejövő NAT-szabályok**

    network lb inbound-nat-rule create [options] <resource-group> <lb-name> <name>
Egy terheléselosztó bejövő NAT-szabályt hoz létre.

Az alábbi példában egy NAT-szabályt az előtérbeli IP-címet (ez korábban már definiálva lett az "azure network frontend-ip" paranccsal), amely a hálózati adatforgalom elküldésére használja a terheléselosztó kimenő port és a bejövő figyelőportja létrehozott.

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

A paraméterbeállításokkal:

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
<BR>

    network lb inbound-nat-rule set [options] <resource-group> <lb-name> <name>
Frissíti egy meglévő bejövő nat-szabályt. A következő példában úgy módosítottuk a bejövő figyel-e port a 80-as a 81-es.

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

A paraméterbeállításokkal:

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
<BR>

    network lb inbound-nat-rule list [options] <resource-group> <lb-name>

Minden, a terheléselosztó bejövő nat-szabályok listája.

    azure network lb inbound-nat-rule list -g myresourcegroup -l mylb

    info:    Executing command network lb inbound-nat-rule list
    + Looking up the load balancer "mylb"
    data:    Name          Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes  Backend IP configuration
    data:    ------------  ------------------  --------  -------------  ------------  ------------------  -----------------------  ---
    ---------------------
    data:    myinboundnat  Succeeded           Tcp       81             8080          false               4

    info:    network lb inbound-nat-rule list command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -s, --subscription <subscription>      the subscription identifier
<BR>

    network lb inbound-nat-rule delete [options] <resource-group> <lb-name> <name>

Egy adott erőforráscsoportban a terheléselosztó NAT-szabály törlése.

    azure network lb inbound-nat-rule delete -g myresourcegroup -l mylb -n myinboundnat

    info:    Executing command network lb inbound-nat-rule delete
    + Looking up the load balancer "mylb"
    Delete inbound NAT rule "myinboundnat?" [y/n] y
    + Updating load balancer "mylb"
    info:    network lb inbound-nat-rule delete command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -l, --lb-name <lb-name>                the name of the load balancer
    -n, --name <name>                      the name of the inbound NAT rule
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier

**Nyilvános ip-címek kezelését végző parancsokról**

    network public-ip create [options] <resource-group> <name> <location>
Egy nyilvános IP-cím erőforrást hoz létre. A nyilvános IP-cím erőforrás létrehoz és rendelheti hozzá az egy tartomány nevét.

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


A paraméterbeállításokkal:

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
<br>

    network public-ip set [options] <resource-group> <name>
Egy meglévő nyilvános IP-cím erőforrás tulajdonságait frissíti. Az alábbi példa úgy módosítottuk a nyilvános IP-címet a dinamikus statikusra.

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

A paraméterbeállításokkal:

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

<br>
    Network public-ip list [kapcsolók] < erőforráscsoport > felsorolja az összes nyilvános IP-erőforrás egy erőforráscsoportban.

    azure network public-ip list -g myresourcegroup

    info:    Executing command network public-ip list
    + Getting the public ip addresses
    data:    Name             Location  Allocation  IP Address    Idle timeout  DNS Name
    data:    ---------------  --------  ----------  ------------  ------------  -------------------------------------------
    data:    mypubip5         westus    Dynamic                   4             "domain name".westus.cloudapp.azure.com
    data:    myPublicIP       eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
    data:    mytestpublicip   eastus    Dynamic                   4             "domain name".eastus.cloudapp.azure.com
    data:    mytestpublicip1  eastus   Static (Static IP address) 4             azureclitest.eastus.cloudapp.azure.com

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -s, --subscription <subscription>      the subscription identifier
<BR>
    Network public-ip show [kapcsolók] < erőforráscsoport > <name>

Egy erőforráscsoporton belül nyilvános IP-cím erőforrás nyilvános IP-cím tulajdonságainak megjelenítése.

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

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the public IP
    -s, --subscription <subscription>      the subscription identifier


    network public-ip delete [options] <resource-group> <name>

Nyilvános IP-cím erőforrás törlése.

    azure network public-ip delete -g group-1 -n mypublicipname
    info:    Executing command network public-ip delete
    + Looking up the public ip "mypublicipname"
    Delete public ip address "mypublicipname"? [y/n] y
    + Deleting public ip address "mypublicipname"
    info:    network public-ip delete command OK

A paraméterbeállításokkal:

    -h, --help                             output usage information
    -v, --verbose                          use verbose output
    --json                                 use json output
    -g, --resource-group <resource-group>  the name of the resource group
    -n, --name <name>                      the name of the public IP
    -q, --quiet                            quiet mode, do not ask for delete confirmation
    -s, --subscription <subscription>      the subscription identifier


**A hálózati adapterek kezelése parancsokkal**

    network nic create [options] <resource-group> <name> <location>
Hálózati adapter (NIC), amely terheléselosztók segítségével, illetve egy virtuális géphez társítja nevű erőforrást hoz létre.

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

A paraméterbeállításokkal:

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
    --subnet-name <subnet-name>                                  the subnet name
    -m, --subnet-vnet-name <subnet-vnet-name>                        the vnet name under which subnet-name exists
    -t, --tags <tags>                                                the comma seperated list of tags.
    Can be multiple. In the format of "name=value".
    Name is required and value is optional.
    For example, -t tag1=value1;tag2
    -s, --subscription <subscription>                                the subscription identifier
    data:
    info:    network nic create command OK

<BR>

    network nic set [options] <resource-group> <name>

    network nic list [options] <resource-group>
    network nic show [options] <resource-group> <name>
    network nic delete [options] <resource-group> <name>

**A hálózati biztonsági csoportok kezelésére szolgáló parancsokat**

    network nsg create [options] <resource-group> <name> <location>
    network nsg set [options] <resource-group> <name>
    network nsg list [options] <resource-group>
    network nsg show [options] <resource-group> <name>
    network nsg delete [options] <resource-group> <name>

**A hálózati biztonságicsoport-szabályok kezelését végző parancsokról**

    network nsg rule create [options] <resource-group> <nsg-name> <name>
    network nsg rule set [options] <resource-group> <nsg-name> <name>
    network nsg rule list [options] <resource-group> <nsg-name>
    network nsg rule show [options] <resource-group> <nsg-name> <name>
    network nsg rule delete [options] <resource-group> <nsg-name> <name>

**Traffic manager-profil kezelésére szolgáló parancsokat**

    network traffic-manager profile create [options] <resource-group> <name>
    network traffic-manager profile set [options] <resource-group> <name>
    network traffic-manager profile list [options] <resource-group>
    network traffic-manager profile show [options] <resource-group> <name>
    network traffic-manager profile delete [options] <resource-group> <name>
    network traffic-manager profile is-dns-available [options] <resource-group> <relative-dns-name>

**Traffic manager-végpontok kezelését végző parancsokról**

    network traffic-manager profile endpoint create [options] <resource-group> <profile-name> <name> <endpoint-location>
    network traffic-manager profile endpoint set [options] <resource-group> <profile-name> <name>
    network traffic-manager profile endpoint delete [options] <resource-group> <profile-name> <name>

**Parancsok kezelheti a virtuális hálózati átjárók**

    network gateway list [options] <resource-group>

## <a name="azure-provider-commands-to-manage-resource-provider-registrations"></a>az Azure szolgáltatói: erőforrás-szolgáltató regisztrációk kezelését végző parancsokról
**Lista jelenleg regisztrált szolgáltatók a Resource Managerben**

    provider list [options]

**A kért szolgáltató névtere részletek megjelenítése**

    provider show [options] <namespace>

**Az előfizetés-szolgáltató regisztrálása**

    provider register [options] <namespace>

**Az előfizetés-szolgáltató regisztrációjának törlése**

    provider unregister [options] <namespace>

## <a name="azure-resource-commands-to-manage-your-resources"></a>Azure-erőforrás: parancsokat az erőforrások kezeléséhez
**Létrehoz egy erőforrást egy erőforráscsoportban**

    resource create [options] <resource-group> <name> <resource-type> <location> <api-version>

**Frissíti egy erőforrás egy erőforráscsoportba tartozó sablon és paraméterek nélkül**

    resource set [options] <resource-group> <name> <resource-type> <properties> <api-version>

**Az erőforrások listája**

    resource list [options] [resource-group]

**Lekérdezi egy erőforrás egy erőforráscsoportba vagy előfizetésbe belül**

    resource show [options] <resource-group> <name> <resource-type> <api-version>

**Töröl egy erőforrást egy erőforráscsoportban**

    resource delete [options] <resource-group> <name> <resource-type> <api-version>

## <a name="azure-role-commands-to-manage-your-azure-roles"></a>Azure szerepkör: az Azure-szerepkörök kezelésére szolgáló parancsokat
**Az összes rendelkezésre álló szerepkör-definíciók beolvasása**

    role list [options]

**Egy szerepkör-definíció beolvasása**

    role show [options] [name]

**A szerepkör-hozzárendelés kezelését végző parancsokról**

    role assignment create [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
    role assignment list [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]
    role assignment delete [options] [objectId] [upn] [mail] [spn] [role] [scope] [resource-group] [resource-type] [resource-name]

## <a name="azure-storage-commands-to-manage-your-storage-objects"></a>az Azure storage: a tárolási objektum kezelését végző parancsokról
**A tárfiókok kezelését végző parancsokról**

    storage account list [options]
    storage account show [options] <name>
    storage account create [options] <name>
    storage account set [options] <name>
    storage account delete [options] <name>

**A Storage-fiók kulcsok kezelésére szolgáló parancsok**

    storage account keys list [options] <name>
    storage account keys renew [options] <name>

**Parancsok a tárolási kapcsolati karakterlánc megjelenítése**

    storage account connectionstring show [options] <name>

**A Storage-tárolók kezelése-parancsok**

    storage container list [options] [prefix]
    storage container show [options] [container]
    storage container create [options] [container]
    storage container delete [options] [container]
    storage container set [options] [container]

**Megosztott kezelését végző parancsokról aláírások a Storage-tároló eléréséhez**

    storage container sas create [options] [container] [permissions] [expiry]

**Parancsok kezelésére tárolt hozzáférési szabályzatok a Storage-tároló**

    storage container policy create [options] [container] [name]
    storage container policy show [options] [container] [name]
    storage container policy list [options] [container]
    storage container policy set [options] [container] [name]
    storage container policy delete [options] [container] [name]

**A Storage-blobokat kezelését végző parancsokról**

    storage blob list [options] [container] [prefix]
    storage blob show [options] [container] [blob]
    storage blob delete [options] [container] [blob]
    storage blob upload [options] [file] [container] [blob]
    storage blob download [options] [container] [blob] [destination]

**A blob kezelését végző parancsokról másolási műveletek**

    storage blob copy start [options] [sourceUri] [destContainer]
    storage blob copy show [options] [container] [blob]
    storage blob copy stop [options] [container] [blob] [copyid]

**Megosztott kezelését végző parancsokról eléréséhez a Storage-blobba aláírása**

    storage blob sas create [options] [container] [blob] [permissions] [expiry]

**Parancsok a tárolófájl-megosztások kezelése**

    storage share create [options] [share]
    storage share show [options] [share]
    storage share delete [options] [share]
    storage share list [options] [prefix]

**A Storage-fájlok kezelését végző parancsokról**

    storage file list [options] [share] [path]
    storage file delete [options] [share] [path]
    storage file upload [options] [source] [share] [path]
    storage file download [options] [share] [path] [destination]

**A tárolási könyvtárának kezelését végző parancsokról**

    storage directory create [options] [share] [path]
    storage directory delete [options] [share] [path]

**A Storage szolgáltatás üzenetsoraiba kezelését végző parancsokról**

    storage queue create [options] [queue]
    storage queue list [options] [prefix]
    storage queue show [options] [queue]
    storage queue delete [options] [queue]

**Megosztott kezelését végző parancsokról hozzáférési aláírások a tárolási üzenetsor**

    storage queue sas create [options] [queue] [permissions] [expiry]

**Parancsok kezelésére tárolt hozzáférési szabályzatok a tárolási üzenetsor**

    storage queue policy create [options] [queue] [name]
    storage queue policy show [options] [queue] [name]
    storage queue policy list [options] [queue]
    storage queue policy set [options] [queue] [name]
    storage queue policy delete [options] [queue] [name]

**Parancsok kezelése a Storage naplózási tulajdonságok**

    storage logging show [options]
    storage logging set [options]

**Parancsok a Storage-mérőszámok tulajdonságainak kezelése**

    storage metrics show [options]
    storage metrics set [options]

**A Storage-táblák kezelését végző parancsokról**

    storage table create [options] [table]
    storage table list [options] [prefix]
    storage table show [options] [table]
    storage table delete [options] [table]

**Megosztott kezelését végző parancsokról eléréséhez a tárolási táblában található**

    storage table sas create [options] [table] [permissions] [expiry]

**Parancsok kezelésére tárolt hozzáférési szabályzatok a Storage-tábla**

    storage table policy create [options] [table] [name]
    storage table policy show [options] [table] [name]
    storage table policy list [options] [table]
    storage table policy set [options] [table] [name]
    storage table policy delete [options] [table] [name]

## <a name="azure-tag-commands-to-manage-your-resource-manager-tag"></a>a címke az Azure: a resource manager-címke kezelését végző parancsokról
**Egy címke hozzáadása**

    tag create [options] <name> <value>

**Távolítsa el az egész címkét vagy egy címke értéke**

    tag delete [options] <name> <value>

**A címke adatait**

    tag list [options]

**Egy címke beolvasása**

    tag show [options] [name]

## <a name="azure-vm-commands-to-manage-your-azure-virtual-machines"></a>az Azure virtuális gép: az Azure virtuális gépek kezelése parancsokkal
**Virtuális gép létrehozása**

    vm create [options] <resource-group> <name> <location> <os-type>

**Az alapértelmezett erőforrásokat a virtuális gép létrehozása**

    vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password

> [!TIP]
> Parancssori felület 0.10-kezdődően megadhat egy rövid alias, például "UbuntuLTS" vagy "Win2012R2Datacenter" a `image-urn` néhány népszerű Piactéri lemezképek. Futtatás `azure help vm quick-create` lehetőségeket. Ezenkívül kezdve, 0.10-ás verzió `azure vm quick-create` Premium szintű tárolást használ alapértelmezés szerint, ha elérhető a kiválasztott régióban.
> 
> 

**A partner a virtuális gépek listája**

    vm list [options]

**Egy virtuális gépet egy erőforráscsoporton belül**

    vm show [options] <resource-group> <name>

**Egy erőforráscsoporton belül egy virtuális gép törlése**

    vm delete [options] <resource-group> <name>

**Egy virtuális gép rendszerének leállítása erőforráscsoporton belül**

    vm stop [options] <resource-group> <name>

**Indítsa újra egy virtuális gépet egy erőforráscsoporton belül**

    vm restart [options] <resource-group> <name>

**Egy erőforráscsoporton belül egy virtuális gép elindítása**

    vm start [options] <resource-group> <name>

**Egy virtuális gép rendszerének leállítása felszabadítja a számítási erőforrásokat és a egy erőforráscsoport belül**

    vm deallocate [options] <resource-group> <name>

**Elérhető virtuálisgép-méretek listázása**

    vm sizes [options]

**A virtuális gép operációsrendszer-lemezkép vagy Virtuálisgép-lemezkép rögzítése**

    vm capture [options] <resource-group> <name> <vhd-name-prefix>

**Állítsa be a virtuális gép állapotát általánosítottra**

    vm generalize [options] <resource-group> <name>

**A virtuális gép példányait tartalmazó nézet beolvasása**

    vm get-instance-view [options] <resource-group> <name>

**Engedélyezi, hogy a virtuális gép távoli asztali hozzáférés vagy SSH beállítások alaphelyzetbe állítása és alaphelyzetbe állíthatja a jelszót a fiókhoz, amelyen a rendszergazda vagy a sudo hatóság**

    vm reset-access [options] <resource-group> <name>

**Frissítse a virtuális Gépet az új adatokkal**

    vm set [options] <resource-group> <name>

**A virtuális gép adatlemezek kezelését végző parancsokról**

    vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]
    vm disk detach [options] <resource-group> <vm-name> <lun>
    vm disk attach [options] <resource-group> <vm-name> [vhd-url]

**Parancsokat a virtuális gép erőforrás-bővítmények kezelése**

    vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>
    vm extension get [options] <resource-group> <vm-name>

**A Docker virtuális gép kezelését végző parancsokról**

    vm docker create [options] <resource-group> <name> <location> <os-type>

**Parancsokat a Virtuálisgép-rendszerképek kezelése**

    vm image list-publishers [options] <location>
    vm image list-offers [options] <location> <publisher>
    vm image list-skus [options] <location> <publisher> <offer>
    vm image list [options] <location> <publisher> [offer] [sku]
