---
title: Azure Files hálózati végpontok konfigurálása | Microsoft Docs
description: A Azure Files hálózati beállításainak áttekintése.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80082506"
---
# <a name="configuring-azure-files-network-endpoints"></a>Azure Files hálózati végpontok konfigurálása
Azure Files két fő típusú végpontot biztosít az Azure-fájlmegosztás eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel rendelkeznek, és bárhonnan elérhetők a világ bármely pontján.
- Saját végpontok, amelyek egy virtuális hálózaton belül találhatók, és rendelkeznek magánhálózati IP-címmel a virtuális hálózat címterület területén belülről.

A nyilvános és a privát végpontok az Azure Storage-fiókban találhatók. A Storage-fiók egy olyan felügyeleti szerkezet, amely egy megosztott tárolót jelöl, amelyben több fájlmegosztást is üzembe helyezhet, valamint más tárolási erőforrásokat, például blob-tárolókat vagy várólistákat.

Ez a cikk azt ismerteti, hogyan konfigurálhatja a Storage-fiók végpontját az Azure-fájlmegosztás közvetlen eléréséhez. A jelen dokumentumban ismertetett részletek többsége arra is vonatkozik, hogy a Azure File Sync hogyan működik együtt a Storage-fiókhoz tartozó nyilvános és magánhálózati végpontokkal, azonban a Azure File Sync központi telepítés hálózatkezelési szempontjaival kapcsolatos további információkért lásd: [Azure file Sync proxy és tűzfal beállításainak konfigurálása](storage-sync-files-firewall-and-proxy.md).

Javasoljuk, hogy olvassa el a [Azure Files hálózati megfontolásokat](storage-files-networking-overview.md) az útmutató elolvasása előtt.

## <a name="prerequisites"></a>Előfeltételek
- Ez a cikk azt feltételezi, hogy már létrehozott egy Azure-előfizetést. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.
- Ez a cikk azt feltételezi, hogy már létrehozott egy Azure-fájlmegosztást egy olyan Storage-fiókban, amelyhez a helyszíni rendszerből szeretne csatlakozni. Az Azure-fájlmegosztás létrehozásáról az [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című témakörben olvashat bővebben.
- Ha Azure PowerShell szeretne használni, [telepítse a legújabb verziót](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Ha az Azure CLI-t szeretné használni, [telepítse a legújabb verziót](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
Ha létrehoz egy privát végpontot a Storage-fiókjához, a következő Azure-erőforrásokat fogja telepíteni:

- **Privát végpont**: a Storage-fiók privát végpontját jelképező Azure-erőforrás. Ezt olyan erőforrásként tekintheti meg, amely összekapcsolja a Storage-fiókot és egy hálózati adaptert.
- **Hálózati adapter (NIC)**: a megadott virtuális hálózaton/alhálózaton belüli magánhálózati IP-címet fenntartó hálózati adapter. Ez ugyanaz az erőforrás, amelyet a rendszer a virtuális gépek telepítésekor üzembe helyez, azonban a virtuális géphez való hozzárendelés helyett a magánhálózati végpont tulajdonosa.
- **Privát DNS-zóna**: Ha korábban még nem telepített privát végpontot ehhez a virtuális hálózathoz, a rendszer új magánhálózati DNS-zónát helyez üzembe a virtuális hálózathoz. Az ebben a DNS-zónában található Storage-fiókhoz tartozó DNS-rekord is létrejön. Ha már telepített egy privát végpontot ebben a virtuális hálózatban, a rendszer új rekordot ad a Storage-fiókhoz a meglévő DNS-zónához. A DNS-zónák üzembe helyezése nem kötelező, azonban kifejezetten ajánlott, és kötelező, ha az Azure-fájlmegosztást egy AD-szolgáltatással vagy a legelső API használatával csatlakoztatja.

> [!Note]  
> Ez a cikk a Storage-fiók DNS-utótagját használja az Azure `core.windows.net`-beli nyilvános régiókban. Ez a kommentár az Azure szuverén felhőkre is vonatkozik, mint például az Azure US government Cloud és az Azure China Cloud – csak a megfelelő utótagokat helyettesíti a környezetében. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Navigáljon ahhoz a Storage-fiókhoz, amelyhez privát végpontot szeretne létrehozni. A Storage-fiók tartalomjegyzékében válassza a **privát végponti kapcsolatok**lehetőséget, majd a **+ privát végpontot** egy új privát végpont létrehozásához. 

![A Storage-fiók tartalomjegyzékében található Private Endpoint Connections elemek képernyőképe](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Az eredményül kapott varázslónak több lapja is van.

Az **alapvető beállítások** panelen válassza ki a saját végpontja számára a kívánt erőforráscsoportot, nevet és régiót. Ezeket a kívánt módon lehet használni, de nem kell megegyezniük a Storage-fiókkal, bár a magánhálózati végpontot ugyanabban a régióban kell létrehoznia, mint azt a virtuális hálózatot, amelyben létre kívánja hozni a privát végpontot.

![A privát végpont létrehozása szakasz alapjai szakaszának képernyőképe](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

Az **erőforrás** panelen válassza a **saját címtárban található Azure-erőforráshoz való kapcsolódáshoz**használandó választógombot. Az **erőforrástípus**területen válassza ki a **Microsoft. Storage/storageAccounts** elemet az erőforrás típushoz. Az **erőforrás** mező annak az Azure-fájlmegosztásnak a Storage-fiókja, amelyhez csatlakozni szeretne. A cél alerőforrás **fájl**, mivel Azure files.

A **konfiguráció** panelen kiválaszthatja azt a virtuális hálózatot és alhálózatot, amelyhez hozzá szeretné adni a privát végpontot. Válassza ki a fent létrehozott virtuális hálózatot. Ki kell választania egy különálló alhálózatot abból az alhálózatból, amelyhez a szolgáltatási végpontot hozzáadta. A konfiguráció panelen a saját DNS-zóna létrehozásához/frissítéséhez szükséges információk is szerepelnek. Azt javasoljuk, hogy az `privatelink.file.core.windows.net` alapértelmezett zónát használja.

![A konfigurációs szakasz képernyőképe](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

A privát végpont létrehozásához kattintson a **felülvizsgálat + létrehozás** gombra. 

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha az [itt](storage-files-networking-dns.md)leírtak szerint konfigurálta a DNS-továbbítást, tesztelje, hogy a saját végpontja megfelelően van-e beállítva. Ehhez futtassa a következő parancsokat a powershellből, a parancssorból vagy a terminálból (Windows, Linux vagy MacOS rendszereken is működik). A nevet a `<storage-account-name>` megfelelő Storage-fiók nevével kell helyettesíteni:

```
nslookup <storage-account-name>.file.core.windows.net
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, `192.168.0.5` ahol a a virtuális hálózat privát végpontjának MAGÁNHÁLÓZATI IP-címe (a Windowshoz megjelenített kimenet):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ha privát végpontot szeretne létrehozni a Storage-fiókhoz, először meg kell adnia egy hivatkozást a Storage-fiókjára, valamint azt a virtuális hálózati alhálózatot, amelyhez hozzá szeretné adni a magánhálózati végpontot. Cserélje `<storage-account-resource-group-name>`le `<storage-account-name>`a `<vnet-resource-group-name>`, `<vnet-name>`,, `<vnet-subnet-name>` , és az alábbit:

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group-name>"
$storageAccountName = "<storage-account-name>"
$virtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$virtualNetworkName = "<vnet-name>"
$subnetName = "<vnet-subnet-name>"

# Get storage account reference, and throw error if it doesn't exist
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction SilentlyContinue

if ($null -eq $storageAccount) {
    $errorMessage = "Storage account $storageAccountName not found "
    $errorMessage += "in resource group $storageAccountResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get virtual network reference, and throw error if it doesn't exist
$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $virtualNetworkName `
        -ErrorAction SilentlyContinue

if ($null -eq $virtualNetwork) {
    $errorMessage = "Virtual network $virtualNetworkName not found "
    $errorMessage += "in resource group $virtualNetworkResourceGroupName."
    Write-Error -Message $errorMessage -ErrorAction Stop
}

# Get reference to virtual network subnet, and throw error if it doesn't exist
$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $virtualNetworkName." `
            -ErrorAction Stop
}
```

Privát végpont létrehozásához létre kell hoznia egy magánhálózati kapcsolati szolgáltatás kapcsolatot a Storage-fiókkal. A magánhálózati kapcsolati szolgáltatás kapcsolata a magánhálózati végpont létrehozásához szükséges bemenet. 

```PowerShell
# Disable private endpoint network policies
$subnet.PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork | Out-Null

# Create a private link service connection to the storage account.
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name "$storageAccountName-Connection" `
        -PrivateLinkServiceId $storageAccount.Id `
        -GroupId "file"

# Create a new private endpoint.
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name "$storageAccountName-PrivateEndpoint" `
        -Location $virtualNetwork.Location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -ErrorAction Stop
```

Az Azure saját DNS-zóna létrehozása lehetővé teszi a Storage-fiók eredeti nevét, például `storageaccount.file.core.windows.net` a virtuális hálózatban lévő magánhálózati IP-címek feloldását. Bár a privát végpontok létrehozásának perspektívája nem kötelező, kifejezetten az Azure-fájlmegosztás Active Directory-rendszerbiztonsági tag használatával történő csatlakoztatásához vagy a REST APIon keresztül történő hozzáféréshez szükséges.  

```PowerShell
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
$storageAccountSuffix = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
$dnsZoneName = "privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
$dnsZone = Get-AzPrivateDnsZone | `
    Where-Object { $_.Name -eq $dnsZoneName } | `
    Where-Object {
        $privateDnsLink = Get-AzPrivateDnsVirtualNetworkLink `
                -ResourceGroupName $_.ResourceGroupName `
                -ZoneName $_.Name `
                -ErrorAction SilentlyContinue
        
        $privateDnsLink.VirtualNetworkId -eq $virtualNetwork.Id
    }

if ($null -eq $dnsZone) {
    # No matching DNS zone attached to virtual network, so create new one.
    $dnsZone = New-AzPrivateDnsZone `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -Name $dnsZoneName `
            -ErrorAction Stop

    $privateDnsLink = New-AzPrivateDnsVirtualNetworkLink `
            -ResourceGroupName $virtualNetworkResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name "$virtualNetworkName-DnsLink" `
            -VirtualNetworkId $virtualNetwork.Id `
            -ErrorAction Stop
}
```

Most, hogy már rendelkezik a saját DNS-zónára mutató hivatkozással, létre kell hoznia egy rekordot a Storage-fiókhoz.

```PowerShell
$privateEndpointIP = $privateEndpoint | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object @{ 
        Name = "NetworkInterfaces"; 
        Expression = { Get-AzNetworkInterface -ResourceId $_.Id } 
    } | `
    Select-Object -ExpandProperty NetworkInterfaces | `
    Select-Object -ExpandProperty IpConfigurations | `
    Select-Object -ExpandProperty PrivateIpAddress

$privateDnsRecordConfig = New-AzPrivateDnsRecordConfig `
        -IPv4Address $privateEndpointIP

New-AzPrivateDnsRecordSet `
        -ResourceGroupName $virtualNetworkResourceGroupName `
        -Name $storageAccountName `
        -RecordType A `
        -ZoneName $dnsZoneName `
        -Ttl 600 `
        -PrivateDnsRecords $privateDnsRecordConfig `
        -ErrorAction Stop | `
    Out-Null
```

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha az [itt](storage-files-networking-dns.md)leírtak szerint konfigurálta a DNS-továbbítást, a következő parancsokkal ellenőrizheti, hogy a saját végpontja megfelelően van-e beállítva:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, `192.168.0.5` ahol a a virtuális hálózat privát végpontjának MAGÁNHÁLÓZATI IP-címe:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Ha privát végpontot szeretne létrehozni a Storage-fiókhoz, először meg kell adnia egy hivatkozást a Storage-fiókjára, valamint azt a virtuális hálózati alhálózatot, amelyhez hozzá szeretné adni a magánhálózati végpontot. Cserélje `<storage-account-resource-group-name>`le `<storage-account-name>`a `<vnet-resource-group-name>`, `<vnet-name>`,, `<vnet-subnet-name>` , és az alábbit:

```bash
storageAccountResourceGroupName="<storage-account-resource-group-name>"
storageAccountName="<storage-account-name>"
virtualNetworkResourceGroupName="<vnet-resource-group-name>"
virtualNetworkName="<vnet-name>"
subnetName="<vnet-subnet-name>"

# Get storage account ID 
storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

# Get virtual network ID
virtualNetwork=$(az network vnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --name $virtualNetworkName \
        --query "id" | \
    tr -d '"')

# Get subnet ID
subnet=$(az network vnet subnet show \
        --resource-group $virtualNetworkResourceGroupName \
        --vnet-name $virtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Privát végpont létrehozásához először győződjön meg arról, hogy az alhálózat magánhálózati végpontjának hálózati házirendje le van tiltva. Ezután létrehozhat egy privát végpontot a `az network private-endpoint create` parancs használatával.

```bash
# Disable private endpoint network policies
az network vnet subnet update \
        --ids $subnet \
        --disable-private-endpoint-network-policies \
        --output none

# Get virtual network location
region=$(az network vnet show \
        --ids $virtualNetwork \
        --query "location" | \
    tr -d '"')

# Create a private endpoint
privateEndpoint=$(az network private-endpoint create \
        --resource-group $virtualNetworkResourceGroupName \
        --name "$storageAccountName-PrivateEndpoint" \
        --location $region \
        --subnet $subnet \
        --private-connection-resource-id $storageAccount \
        --group-ids "file" \
        --connection-name "$storageAccountName-Connection" \
        --query "id" | \
    tr -d '"')
```

Az Azure saját DNS-zóna létrehozása lehetővé teszi a Storage-fiók eredeti nevét, például `storageaccount.file.core.windows.net` a virtuális hálózatban lévő magánhálózati IP-címek feloldását. Bár a privát végpontok létrehozásának perspektívája nem kötelező, kifejezetten az Azure-fájlmegosztás Active Directory-rendszerbiztonsági tag használatával történő csatlakoztatásához vagy a REST APIon keresztül történő hozzáféréshez szükséges.  

```bash
# Get the desired storage account suffix (core.windows.net for public cloud).
# This is done like this so this script will seamlessly work for non-public Azure.
storageAccountSuffix=$(az cloud show \
        --query "suffixes.storageEndpoint" | \
    tr -d '"')

# For public cloud, this will generate the following DNS suffix:
# privatelink.file.core.windows.net.
dnsZoneName="privatelink.file.$storageAccountSuffix"

# Find a DNS zone matching desired name attached to this virtual network.
possibleDnsZones=$(az network private-dns zone list \
        --query "[?name == '$dnsZoneName'].id" \
        --output tsv)

for possibleDnsZone in $possibleDnsZones
do
    possibleResourceGroupName=$(az resource show \
            --ids $possibleDnsZone \
            --query "resourceGroup" | \
        tr -d '"')
    
    link=$(az network private-dns link vnet list \
            --resource-group $possibleResourceGroupName \
            --zone-name $dnsZoneName \
            --query "[?virtualNetwork.id == '$virtualNetwork'].id" \
            --output tsv)
    
    if [ -z $link ]
    then
        1 > /dev/null
    else 
        dnsZoneResourceGroup=$possibleResourceGroupName
        dnsZone=$possibleDnsZone
        break
    fi  
done

if [ -z $dnsZone ]
then
    # No matching DNS zone attached to virtual network, so create a new one
    dnsZone=$(az network private-dns zone create \
            --resource-group $virtualNetworkResourceGroupName \
            --name $dnsZoneName \
            --query "id" | \
        tr -d '"')
    
    az network private-dns link vnet create \
            --resource-group $resourceGroupName \
            --zone-name $zoneName \
            --name "$virtualNetworkName-DnsLink" \
            --virtual-network $virtualNetwork \
            --registration-enabled false \
            --output none
fi
```

Most, hogy már rendelkezik a saját DNS-zónára mutató hivatkozással, létre kell hoznia egy rekordot a Storage-fiókhoz.

```bash
privateEndpointNIC=$(az network private-endpoint show \
        --ids $privateEndpoint \
        --query "networkInterfaces[0].id" | \
    tr -d '"')

privateEndpointIP=$(az network nic show \
        --ids $privateEndpointNIC \
        --query "ipConfigurations[0].privateIpAddress" | \
    tr -d '"')

az network private-dns record-set a create \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --name $storageAccountName \
        --output none

az network private-dns record-set a add-record \
        --resource-group $dnsZoneResourceGroup \
        --zone-name $dnsZoneName \
        --record-set-name $storageAccountName \
        --ipv4-address $privateEndpointIP \
        --output none
```

Ha a virtuális hálózatán belül van egy virtuális gép, vagy ha az [itt](storage-files-networking-dns.md)leírtak szerint konfigurálta a DNS-továbbítást, a következő parancsokkal ellenőrizheti, hogy a saját végpontja megfelelően van-e beállítva:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Ha minden sikeresen működött, a következő kimenetnek kell megjelennie, `192.168.0.5` ahol a a virtuális hálózat privát végpontjának MAGÁNHÁLÓZATI IP-címe:

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

## <a name="restrict-access-to-the-public-endpoint"></a>A nyilvános végponthoz való hozzáférés korlátozása
A nyilvános végponthoz való hozzáférést a Storage-fiók tűzfalának beállításai alapján korlátozhatja. Általánosságban elmondható, hogy egy Storage-fiókhoz a legtöbb tűzfal-házirend korlátozza a hálózati hozzáférést egy vagy több virtuális hálózathoz. A Storage-fiókokhoz való hozzáférés korlátozásának két módja van:

- [Hozzon létre egy vagy több privát végpontot a Storage-fiókhoz](#create-a-private-endpoint) , és korlátozza a nyilvános végponthoz való hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokból származó forgalom férhessenek hozzá az Azure-fájlmegosztás számára a Storage-fiókon belül.
- Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra. Ez a *szolgáltatás végpontok*nevű virtuális hálózat funkciójának használatával működik. Ha egy szolgáltatási végponton keresztül korlátozza a forgalmat egy Storage-fiókra, akkor továbbra is a nyilvános IP-cím használatával fér hozzá a Storage-fiókhoz.

### <a name="restrict-all-access-to-the-public-endpoint"></a>A nyilvános végponthoz való összes hozzáférés korlátozása
Ha a nyilvános végponthoz való minden hozzáférés korlátozott, a Storage-fiók továbbra is elérhető a privát végpontokon keresztül. Ellenkező esetben a rendszer elutasítja a Storage-fiók nyilvános végpontjának érvényes kérelmeit. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Navigáljon ahhoz a Storage-fiókhoz, amelynek a nyilvános végponthoz való hozzáférését korlátozni szeretné. A Storage-fiók tartalomjegyzékében válassza a **tűzfalak és virtuális hálózatok**lehetőséget.

A lap tetején válassza a **kiválasztott hálózatok** választógombot. Ezzel a beállítással a nyilvános végpont korlátozásának szabályozására nem kerül sor. Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a szolgáltatásfiók** -hoz, hogy a megbízható Microsoft-szolgáltatások, például a Azure file Sync a Storage-fiók elérését.

![Képernyőkép a tűzfalakról és a virtuális hálózatokról a megfelelő korlátozásokkal](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A következő PowerShell-parancs letiltja a Storage-fiók nyilvános végpontján lévő összes forgalmat. Vegye figyelembe, hogy a parancshoz a `-Bypass` paraméter `AzureServices`van beállítva. Ez lehetővé teszi, hogy a megbízható, első féltől származó szolgáltatások, például a Azure File Sync hozzáférhessenek a Storage-fiókhoz a nyilvános végponton keresztül.

```PowerShell
# This assumes $storageAccount is still defined from the beginning of this of this guide.
$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
A következő CLI-parancs letiltja a Storage-fiók nyilvános végpontján lévő összes forgalmat. Vegye figyelembe, hogy a parancshoz a `-bypass` paraméter `AzureServices`van beállítva. Ez lehetővé teszi, hogy a megbízható, első féltől származó szolgáltatások, például a Azure File Sync hozzáférhessenek a Storage-fiókhoz a nyilvános végponton keresztül.

```bash
# This assumes $storageAccountResourceGroupName and $storageAccountName 
# are still defined from the beginning of this guide.
az storage account update \
    --resource-group $storageAccountResourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" \
    --output none
```
---

### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>A nyilvános végponthoz való hozzáférés korlátozása adott virtuális hálózatokra
Ha a Storage-fiókot adott virtuális hálózatokra korlátozza, a megadott virtuális hálózatokról érkező kéréseket engedélyez a nyilvános végpontnak. Ez a *szolgáltatás végpontok*nevű virtuális hálózat funkciójának használatával működik. Ez privát végpontokkal vagy anélkül is használható.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Navigáljon ahhoz a Storage-fiókhoz, amelyhez a nyilvános végpontot adott virtuális hálózatokra szeretné korlátozni. A Storage-fiók tartalomjegyzékében válassza a **tűzfalak és virtuális hálózatok**lehetőséget. 

A lap tetején válassza a **kiválasztott hálózatok** választógombot. Ezzel a beállítással a nyilvános végpont korlátozásának szabályozására nem kerül sor. Kattintson a **+ meglévő virtuális hálózat hozzáadása** lehetőségre azon adott virtuális hálózat kiválasztásához, amely számára engedélyezni kell a Storage-fiók nyilvános végponton keresztüli elérését. Ehhez ki kell választania egy virtuális hálózatot és egy alhálózatot a virtuális hálózathoz. 

Győződjön meg arról, hogy a **megbízható Microsoft-szolgáltatások hozzáférhetnek ehhez a szolgáltatásfiók** -hoz, hogy a megbízható Microsoft-szolgáltatások, például a Azure file Sync a Storage-fiók elérését.

![Képernyőfelvétel a tűzfalakról és a virtuális hálózatokról panel egy adott virtuális hálózattal, amely lehetővé teszi a Storage-fiók elérését a nyilvános végponton keresztül](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A Storage-fiók nyilvános végpontjának a szolgáltatási végpontokat használó meghatározott virtuális hálózatokra való hozzáférésének korlátozásához először össze kell gyűjtenie a Storage-fiók és a virtuális hálózat adatait. Töltse ki `<storage-account-resource-group>`a `<storage-account-name>` `<vnet-resource-group-name>` `<vnet-name>`,,,, `<subnet-name>` és az adatokat az adatok gyűjtéséhez.

```PowerShell
$storageAccountResourceGroupName = "<storage-account-resource-group>"
$storageAccountName = "<storage-account-name>"
$restrictToVirtualNetworkResourceGroupName = "<vnet-resource-group-name>"
$restrictToVirtualNetworkName = "<vnet-name>"
$subnetName = "<subnet-name>"

$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $storageAccountResourceGroupName `
        -Name $storageAccountName `
        -ErrorAction Stop

$virtualNetwork = Get-AzVirtualNetwork `
        -ResourceGroupName $restrictToVirtualNetworkResourceGroupName `
        -Name $restrictToVirtualNetworkName `
        -ErrorAction Stop

$subnet = $virtualNetwork | `
    Select-Object -ExpandProperty Subnets | `
    Where-Object { $_.Name -eq $subnetName }

if ($null -eq $subnet) {
    Write-Error `
            -Message "Subnet $subnetName not found in virtual network $restrictToVirtualNetworkName." `
            -ErrorAction Stop
}
```

Ahhoz, hogy az Azure hálózati háló a virtuális hálózatról érkező forgalmat a Storage-fiók nyilvános végpontjának beszerzéséhez engedélyezi, a virtuális hálózat alhálózatának elérhetőnek `Microsoft.Storage` kell lennie a szolgáltatás végpontjának. A következő PowerShell-parancsok hozzáadja a `Microsoft.Storage` szolgáltatás-végpontot az alhálózathoz, ha még nem létezik.

```PowerShell
$serviceEndpoints = $subnet | `
    Select-Object -ExpandProperty ServiceEndpoints | `
    Select-Object -ExpandProperty Service

if ($serviceEndpoints -notcontains "Microsoft.Storage") {
    if ($null -eq $serviceEndpoints) {
        $serviceEndpoints = @("Microsoft.Storage")
    } elseif ($serviceEndpoints -is [string]) {
        $serviceEndpoints = @($serviceEndpoints, "Microsoft.Storage")
    } else {
        $serviceEndpoints += "Microsoft.Storage"
    }

    $virtualNetwork = $virtualNetwork | Set-AzVirtualNetworkSubnetConfig `
            -Name $subnetName `
            -AddressPrefix $subnet.AddressPrefix `
            -ServiceEndpoint $serviceEndpoints `
            -WarningAction SilentlyContinue `
            -ErrorAction Stop | `
        Set-AzVirtualNetwork `
            -ErrorAction Stop
}
```

A Storage-fiók felé irányuló forgalom korlátozásának utolsó lépése egy hálózati szabály létrehozása, és a Storage-fiók hálózati szabályának beállítása.

```PowerShell
$networkRule = $storageAccount | Add-AzStorageAccountNetworkRule `
    -VirtualNetworkResourceId $subnet.Id `
    -ErrorAction Stop

$storageAccount | Update-AzStorageAccountNetworkRuleSet `
        -DefaultAction Deny `
        -Bypass AzureServices `
        -VirtualNetworkRule $networkRule `
        -WarningAction SilentlyContinue `
        -ErrorAction Stop | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
A Storage-fiók nyilvános végpontjának a szolgáltatási végpontokat használó meghatározott virtuális hálózatokra való hozzáférésének korlátozásához először össze kell gyűjtenie a Storage-fiók és a virtuális hálózat adatait. Töltse ki `<storage-account-resource-group>`a `<storage-account-name>` `<vnet-resource-group-name>` `<vnet-name>`,,,, `<subnet-name>` és az adatokat az adatok gyűjtéséhez.

```bash
storageAccountResourceGroupName="<storage-account-resource-group>"
storageAccountName="<storage-account-name>"
restrictToVirtualNetworkResourceGroupName="<vnet-resource-group-name>"
restrictToVirtualNetworkName="<vnet-name>"
subnetName="<subnet-name>"

storageAccount=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "id" | \
    tr -d '"')

virtualNetwork=$(az network vnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --name $restrictToVirtualNetworkName \
        --query "id" | \
    tr -d '"')

subnet=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "id" | \
    tr -d '"')
```

Ahhoz, hogy az Azure hálózati háló a virtuális hálózatról érkező forgalmat a Storage-fiók nyilvános végpontjának beszerzéséhez engedélyezi, a virtuális hálózat alhálózatának elérhetőnek `Microsoft.Storage` kell lennie a szolgáltatás végpontjának. A következő CLI-parancsok hozzáadja a `Microsoft.Storage` szolgáltatás-végpontot az alhálózathoz, ha még nem létezik.

```bash
serviceEndpoints=$(az network vnet subnet show \
        --resource-group $restrictToVirtualNetworkResourceGroupName \
        --vnet-name $restrictToVirtualNetworkName \
        --name $subnetName \
        --query "serviceEndpoints[].service" \
        --output tsv)

foundStorageServiceEndpoint=false
for serviceEndpoint in $serviceEndpoints
do
    if [ $serviceEndpoint = "Microsoft.Storage" ]
    then
        foundStorageServiceEndpoint=true
    fi
done

if [ $foundStorageServiceEndpoint = false ] 
then
    serviceEndpointList=""

    for serviceEndpoint in $serviceEndpoints
    do
        serviceEndpointList+=$serviceEndpoint
        serviceEndpointList+=" "
    done
    
    serviceEndpointList+="Microsoft.Storage"

    az network vnet subnet update \
            --ids $subnet \
            --service-endpoints $serviceEndpointList \
            --output none
fi
```

A Storage-fiók felé irányuló forgalom korlátozásának utolsó lépése egy hálózati szabály létrehozása, és a Storage-fiók hálózati szabályának beállítása.

```bash
az storage account network-rule add \
        --resource-group $storageAccountResourceGroupName \
        --account-name $storageAccountName \
        --subnet $subnet \
        --output none

az storage account update \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --bypass "AzureServices" \
        --default-action "Deny" \
        --output none
```

---

## <a name="see-also"></a>Lásd még
- [Azure Files hálózati megfontolások](storage-files-networking-overview.md)
- [DNS-továbbítás konfigurálása az Azure Fileshoz](storage-files-networking-dns.md)
- [S2S VPN konfigurálása Azure Fileshoz](storage-files-configure-s2s-vpn.md)