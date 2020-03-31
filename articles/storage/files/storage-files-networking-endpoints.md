---
title: Az Azure Files hálózati végpontjainak konfigurálása | Microsoft dokumentumok
description: Az Azure Files hálózati lehetőségeinek áttekintése.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cc487e8def180735606aa010651dde40ef93908e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80082506"
---
# <a name="configuring-azure-files-network-endpoints"></a>Az Azure Files hálózati végpontjainak konfigurálása
Az Azure Files kétféle végpontot biztosít az Azure-fájlmegosztások eléréséhez: 
- Nyilvános végpontok, amelyek nyilvános IP-címmel rendelkeznek, és a világ bármely pontjáról elérhetők.
- Privát végpontok, amelyek egy virtuális hálózaton belül léteznek, és a virtuális hálózat címteréből rendelkeznek privát IP-címmel.

Nyilvános és privát végpontok léteznek az Azure storage-fiókban. A tárfiók egy felügyeleti konstrukció, amely egy megosztott tárolókészletet képvisel, amelyben több fájlmegosztást, valamint egyéb tárolási erőforrásokat, például blobtárolókat vagy várólistákat telepíthet.

Ez a cikk arra összpontosít, hogyan konfigurálhatja a tárfiók végpontjait az Azure-fájlmegosztás közvetlen eléréséhez. A jelen dokumentumban megadott részletek nagy része arra is vonatkozik, hogy az Azure File Sync hogyan működik együtt a tárfiók nyilvános és privát végpontjaival, azonban az Azure File Sync központi telepítésével kapcsolatos hálózati szempontokról az [Azure File Sync proxy és a tűzfal beállításainak konfigurálása](storage-sync-files-firewall-and-proxy.md)című témakörben olvashat bővebben.

Azt javasoljuk, hogy olvassa el [az Azure Files hálózati szempontok at,](storage-files-networking-overview.md) mielőtt elolvassa ezt az útmutatót.

## <a name="prerequisites"></a>Előfeltételek
- Ez a cikk feltételezi, hogy már létrehozott egy Azure-előfizetést. Ha még nem rendelkezik előfizetéssel, akkor a kezdés előtt hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Ez a cikk feltételezi, hogy már létrehozott egy Azure-fájlmegosztást egy tárfiókban, amelyhez a helyszíni kapcsolatról szeretne csatlakozni. Az Azure-fájlmegosztás létrehozásáról az [Azure-fájlmegosztás létrehozása című](storage-how-to-create-file-share.md)témakörben olvashat.
- Ha az Azure PowerShellt kívánja használni, [telepítse a legújabb verziót.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Ha az Azure CLI-t kívánja használni, [telepítse a legújabb verziót.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
Ha privát végpontot hoz létre a tárfiókhoz, a következő Azure-erőforrások üzembe helyezése lesz:

- **Egy privát végpont:** a tárfiók saját végpontját képviselő Azure-erőforrás. Ezt úgy is felhasználhatja, mint egy olyan erőforrást, amely egy tárfiókot és egy hálózati felületet kapcsol össze.
- **Hálózati adapter :** Az a hálózati adapter, amely a megadott virtuális hálózaton/alhálózaton belül magánhálózati IP-címet tart fenn. Ez pontosan ugyanaz az erőforrás, amely egy virtuális gép üzembe helyezésekor üzembe kerül, de ahelyett, hogy egy virtuális géphez lenne rendelve, a privát végpont tulajdonában van.
- **MagánDNS-zóna:** Ha még soha nem telepített magánvégpontot ehhez a virtuális hálózathoz, akkor egy új privát DNS-zóna lesz telepítve a virtuális hálózathoz. A DNS A rekord is létrejön a tárfiók ebben a DNS-zónában. Ha már telepített egy privát végpontot ebben a virtuális hálózatban, a tárfiók új A rekordja kerül a meglévő DNS-zónába. A DNS-zóna telepítése nem kötelező, de erősen ajánlott, és szükséges, ha az Azure-fájlmegosztások egy AD szolgáltatás egyszerű, vagy a FileREST API használatával.

> [!Note]  
> Ez a cikk a tárfiók DNS-utótagja `core.windows.net`az Azure nyilvános régiók, . Ez a kommentár az Azure Sovereign felhőkre is vonatkozik, például az Azure US Government felhőre és az Azure China felhőre – csak helyettesítse a megfelelő utótagokat a környezetéhez. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Keresse meg azt a tárfiókot, amelyhez privát végpontot szeretne létrehozni. A tárfiók tartalomjegyzékében válassza a **Privát végpontkapcsolatok**lehetőséget, majd **a + Privát végpontot** egy új magánvégpont létrehozásához. 

![Képernyőkép a magánszintű végpontkapcsolatok eleméről a tárfiók tartalomjegyzékében](media/storage-files-networking-endpoints/create-private-endpoint-0.png)

Az eredményül kapott varázslónak több lapot kell kitöltenie.

Az **Alapok** panelen válassza ki a kívánt erőforráscsoportot, nevet és régiót a privát végponthoz. Ezek lehetnek, amit akar, nem kell egyeznie a tárfiók egyébként, bár létre kell hoznia a privát végpont ugyanabban a régióban, mint a virtuális hálózat létre kívánja hozni a privát végpontot.

![A Saját szintű végpont létrehozása szakasz Alapjai szakaszának képernyőképe](media/storage-files-networking-endpoints/create-private-endpoint-1.png)

Az **Erőforrás** panelen válassza ki a **címtárban lévő Azure-erőforráshoz való csatlakozás**választógombját. Az **Erőforrástípusa csoportban**válassza a **Microsoft.Storage/storageAccounts elemet** az erőforrástípushoz. Az **Erőforrás** mező az az Azure-fájlmegosztással rendelkező tárfiók, amelyhez csatlakozni kíván. A cél alerőforrás **a fájl,** mivel ez az Azure Files.

A **Konfiguráció** panel lehetővé teszi, hogy válassza ki az adott virtuális hálózat és alhálózat szeretné hozzáadni a saját végpontot. Válassza ki a fent létrehozott virtuális hálózatot. Ki kell választania egy külön alhálózatot abból az alhálózatból, amelyhez a szolgáltatásvégpontot hozzáadta. A Konfiguráció panel a privát DNS-zóna létrehozásához/frissítéséhez szükséges információkat is tartalmazza. Az alapértelmezett `privatelink.file.core.windows.net` zóna használatát javasoljuk.

![Képernyőkép a Konfiguráció szakaszról](media/storage-files-networking-endpoints/create-private-endpoint-2.png)

Kattintson **a Véleményezés + létrehozás** gombra a saját végpont létrehozásához. 

Ha a virtuális hálózaton belül van egy virtuális gép, vagy [az itt](storage-files-networking-dns.md)leírtak szerint konfigurálta a DNS-továbbítást, tesztelheti, hogy a privát végpont megfelelően lett-e beállítva a Következő parancsok futtatásával a PowerShellből, a parancssorból vagy a terminálból (Windows, Linux vagy macOS rendszeren működik). Le kell `<storage-account-name>` cserélnie a megfelelő tárfiók névre:

```
nslookup <storage-account-name>.file.core.windows.net
```

Ha minden sikeresen működött, a következő `192.168.0.5` kimenetet kell látnia, hol található a virtuális hálózat privát végpontjának privát IP-címe (windowsos kimenet):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Privát végpont létrehozásához a tárfiókhoz először meg kell szereznie egy hivatkozást a tárfiókra és a virtuális hálózati alhálózatra, amelyhez hozzá szeretné adni a privát végpontot. Csere `<storage-account-resource-group-name>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<vnet-subnet-name>` , , és az alábbiakban:

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

Privát végpont létrehozásához létre kell hoznia egy privát kapcsolat szolgáltatás kapcsolatot a tárfiókkal. A privát kapcsolat szolgáltatás kapcsolat a magán-végpont létrehozásának bemenete. 

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

Az Azure-beli privát DNS-zóna létrehozása lehetővé `storageaccount.file.core.windows.net` teszi a tárfiók eredeti nevét, például a virtuális hálózaton belüli privát IP-címre való feloldást. Bár nem kötelező a magán-végpont létrehozása szempontjából, kifejezetten szükséges az Azure-fájlmegosztás csatlakoztatása egy AD-felhasználó egyszerű vagy a REST API-n keresztül érhető el.  

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

Most, hogy már rendelkezik a privát DNS-zónára, létre kell hoznia egy A rekordot a tárfiókhoz.

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

Ha a virtuális hálózaton belül van egy virtuális gép, vagy [az itt](storage-files-networking-dns.md)leírtak szerint konfigurálta a DNS-továbbítást, tesztelheti, hogy a privát végpont megfelelően lett-e beállítva a következő parancsokkal:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.File) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Ha minden sikeresen működött, a következő `192.168.0.5` kimenetet kell látnia, hol található a virtuális hálózat privát IP-címe:

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
Privát végpont létrehozásához a tárfiókhoz először meg kell szereznie egy hivatkozást a tárfiókra és a virtuális hálózati alhálózatra, amelyhez hozzá szeretné adni a privát végpontot. Csere `<storage-account-resource-group-name>` `<storage-account-name>`, `<vnet-resource-group-name>` `<vnet-name>`, `<vnet-subnet-name>` , , és az alábbiakban:

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

Privát végpont létrehozásához először győződjön meg arról, hogy az alhálózat privát végponthálózati házirendje le van tiltva. Ezután létrehozhat egy privát végpontot a `az network private-endpoint create` paranccsal

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

Az Azure-beli privát DNS-zóna létrehozása lehetővé `storageaccount.file.core.windows.net` teszi a tárfiók eredeti nevét, például a virtuális hálózaton belüli privát IP-címre való feloldást. Bár nem kötelező a magán-végpont létrehozása szempontjából, kifejezetten szükséges az Azure-fájlmegosztás csatlakoztatása egy AD-felhasználó egyszerű vagy a REST API-n keresztül érhető el.  

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

Most, hogy már rendelkezik a privát DNS-zónára, létre kell hoznia egy A rekordot a tárfiókhoz.

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

Ha a virtuális hálózaton belül van egy virtuális gép, vagy [az itt](storage-files-networking-dns.md)leírtak szerint konfigurálta a DNS-továbbítást, tesztelheti, hogy a privát végpont megfelelően lett-e beállítva a következő parancsokkal:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.File" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Ha minden sikeresen működött, a következő `192.168.0.5` kimenetet kell látnia, hol található a virtuális hálózat privát IP-címe:

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
Korlátozhatja a hozzáférést a nyilvános végponthoz a tárfiók tűzfalbeállításaival. A tárfiók legtöbb tűzfalházirendje általában korlátozza a hálózati hozzáférést egy vagy több virtuális hálózathoz. A tárfiókhoz való hozzáférés virtuális hálózathoz való korlátozásának két megközelítése van:

- [Hozzon létre egy vagy több privát végpontot a tárfiókhoz,](#create-a-private-endpoint) és korlátozza a nyilvános végponthoz való összes hozzáférést. Ez biztosítja, hogy csak a kívánt virtuális hálózatokból származó forgalom férhet hozzá az Azure-fájlmegosztások a tárfiókon belül.
- Korlátozza a nyilvános végpontot egy vagy több virtuális hálózatra. Ez a *szolgáltatásvégpontok*nevű virtuális hálózat képességének használatával működik. Ha korlátozza a forgalmat egy szolgáltatásvégponton keresztül egy tárfiókra, továbbra is a nyilvános IP-címen keresztül éri el a tárfiókot.

### <a name="restrict-all-access-to-the-public-endpoint"></a>A nyilvános végponthoz való hozzáférés korlátozása
Ha a nyilvános végponthoz való hozzáférés korlátozott, a tárfiók továbbra is elérhető a privát végpontokon keresztül. Ellenkező esetben a tárfiók nyilvános végpontjára irányuló érvényes kérelmeket a rendszer elutasítja. 

# <a name="portal"></a>[Portál](#tab/azure-portal)
Keresse meg azt a tárfiókot, amelyhez korlátozni szeretné a nyilvános végponthoz való összes hozzáférést. A tárfiók tartalomjegyzékében válassza a **Tűzfalak és a virtuális hálózatok**lehetőséget.

A lap tetején válassza a **Kijelölt hálózatok** választógombot. Ez megszünteti a nyilvános végpont korlátozásának szabályozására szolgáló beállítások at. Jelölje be **A megbízható Microsoft-szolgáltatások számára a szolgáltatásfiók elérésének engedélyezése** jelölőnégyzetet, hogy a megbízható, első féltől származó Microsoft-szolgáltatások, például az Azure File Sync hozzáférhessenek a tárfiókhoz.

![Képernyőkép a Tűzfalak és a virtuális hálózatok panelről a megfelelő korlátozásokkal a helyén](media/storage-files-networking-endpoints/restrict-public-endpoint-0.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
A következő PowerShell-parancs megtagadja a tárfiók nyilvános végpontjára irányuló összes forgalmat. Ne feledje, `-Bypass` hogy ez `AzureServices`a parancs paramétere a . Ez lehetővé teszi a megbízható, első féltől származó szolgáltatások, például az Azure File Sync számára, hogy a nyilvános végponton keresztül hozzáférjenek a tárfiókhoz.

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
A következő CLI parancs megtagadja a tárfiók nyilvános végpontjára irányuló összes forgalmat. Ne feledje, `-bypass` hogy ez `AzureServices`a parancs paramétere a . Ez lehetővé teszi a megbízható, első féltől származó szolgáltatások, például az Azure File Sync számára, hogy a nyilvános végponton keresztül hozzáférjenek a tárfiókhoz.

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
Ha a tárfiókot adott virtuális hálózatokra korlátozza, a megadott virtuális hálózatokon belül a nyilvános végpontra irányuló kéréseket engedélyezi. Ez a *szolgáltatásvégpontok*nevű virtuális hálózat képességének használatával működik. Ez használható saját végponttal vagy anélkül.

# <a name="portal"></a>[Portál](#tab/azure-portal)
Keresse meg azt a tárfiókot, amelynek a nyilvános végpontját adott virtuális hálózatokra szeretné korlátozni. A tárfiók tartalomjegyzékében válassza a **Tűzfalak és a virtuális hálózatok**lehetőséget. 

A lap tetején válassza a **Kijelölt hálózatok** választógombot. Ez megszünteti a nyilvános végpont korlátozásának szabályozására szolgáló beállítások at. Kattintson **a +Meglévő virtuális hálózat hozzáadása** gombra, és válassza ki azt a virtuális hálózatot, amelyet engedélyezni kell a tárfiók nyilvános végponton keresztül való eléréséhez. Ehhez ki kell jelölni egy virtuális hálózatot és egy alhálózatot az adott virtuális hálózathoz. 

Jelölje be **A megbízható Microsoft-szolgáltatások számára a szolgáltatásfiók elérésének engedélyezése** jelölőnégyzetet, hogy a megbízható, első féltől származó Microsoft-szolgáltatások, például az Azure File Sync hozzáférhessenek a tárfiókhoz.

![Képernyőkép a tűzfalak ról és a virtuális hálózatok panelről, amelyen egy adott virtuális hálózat hozzáférhet a tárfiókhoz a nyilvános végponton keresztül](media/storage-files-networking-endpoints/restrict-public-endpoint-1.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
A tárfiók nyilvános végpontjához való hozzáférés korlátozásához a szolgáltatásvégpontok használatával adott virtuális hálózatokhoz először adatokat kell gyűjtenünk a tárfiókról és a virtuális hálózatról. Töltse `<storage-account-resource-group>`ki `<storage-account-name>` `<vnet-resource-group-name>`a `<vnet-name>`, `<subnet-name>` , , majd az adatok gyűjtését.

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

Annak érdekében, hogy a virtuális hálózatból érkező forgalmat az Azure hálózati háló lehetővé tegye a tárfiók nyilvános végpontjának eléréséhez, a virtuális hálózat alhálózatának rendelkeznie kell a `Microsoft.Storage` szolgáltatásvégpontgal. A következő PowerShell-parancsok `Microsoft.Storage` hozzáadják a szolgáltatásvégpontot az alhálózathoz, ha az még nincs meg.

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

Az utolsó lépés a forgalom korlátozása a tárfiókhoz, hogy hozzon létre egy hálózati szabályt, és adja hozzá a tárfiók hálózati szabálykészletét.

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
A tárfiók nyilvános végpontjához való hozzáférés korlátozásához a szolgáltatásvégpontok használatával adott virtuális hálózatokhoz először adatokat kell gyűjtenünk a tárfiókról és a virtuális hálózatról. Töltse `<storage-account-resource-group>`ki `<storage-account-name>` `<vnet-resource-group-name>`a `<vnet-name>`, `<subnet-name>` , , majd az adatok gyűjtését.

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

Annak érdekében, hogy a virtuális hálózatból érkező forgalmat az Azure hálózati háló lehetővé tegye a tárfiók nyilvános végpontjának eléréséhez, a virtuális hálózat alhálózatának rendelkeznie kell a `Microsoft.Storage` szolgáltatásvégpontgal. A következő CLI-parancsok `Microsoft.Storage` hozzáadják a szolgáltatásvégpontot az alhálózathoz, ha az még nem létezik.

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

Az utolsó lépés a forgalom korlátozása a tárfiókhoz, hogy hozzon létre egy hálózati szabályt, és adja hozzá a tárfiók hálózati szabálykészletét.

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
- [Az Azure Files hálózati szempontjai](storage-files-networking-overview.md)
- [DNS-továbbítás konfigurálása Azure-fájlokhoz](storage-files-networking-dns.md)
- [Az S2S VPN konfigurálása Az Azure-fájlokhoz](storage-files-configure-s2s-vpn.md)