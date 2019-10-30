---
title: Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Fileshoz való használathoz | Microsoft Docs
description: Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Fileshoz való használatra
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141744"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Pont – hely (P2S) VPN konfigurálása Linux rendszeren a Azure Files-vel való használatra
A pont – hely (P2S) VPN-kapcsolattal Azure-fájlmegosztás az Azure-on kívülről is csatlakoztatható SMB-n keresztül, a 445-es port megnyitása nélkül. A pont – hely VPN-kapcsolat az Azure és az egyes ügyfelek közötti VPN-kapcsolat. Ha a P2S VPN-kapcsolatot Azure Files használatával szeretné használni, konfigurálnia kell egy P2S VPN-kapcsolatot minden olyan ügyfél számára, amelyhez csatlakozni szeretne. Ha sok ügyféllel kell csatlakoznia az Azure-fájlmegosztás számára a helyszíni hálózatból, akkor az egyes ügyfelek pont – hely kapcsolata helyett használhat helyek közötti (S2S) VPN-kapcsolatot. További információ: helyek közötti [VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md).

Javasoljuk, hogy olvassa el [Azure Files hálózatkezelési áttekintést](storage-files-networking-overview.md) , mielőtt folytatná ezt a cikket a Azure Files elérhető hálózati beállítások teljes körű megvitatására.

A cikk részletesen bemutatja, hogyan konfigurálhat egy pont – hely VPN-t Linux rendszeren az Azure-fájlmegosztás közvetlen helyszíni csatlakoztatásához. Ha VPN-en keresztül szeretné átirányítani Azure File Sync forgalmat, tekintse meg a [Azure file Sync proxy-és tűzfalbeállítások konfigurálása](storage-sync-files-firewall-and-proxy.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek
- Az Azure CLI legújabb verziója. Az Azure CLI telepítésével kapcsolatos további információkért lásd: [install the Azure POWERSHELL CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) , és válassza ki az operációs rendszert. Ha a Linux rendszeren szeretné használni a Azure PowerShell-modult, akkor az alábbi utasításokat azonban az Azure CLI-hez mutatjuk be.

- Egy Azure-fájlmegosztás, amelyet a helyszínen kíván csatlakoztatni. A pont – hely VPN használatával [standard](storage-how-to-create-file-share.md) vagy [prémium szintű Azure-fájlmegosztás](storage-how-to-create-premium-fileshare.md) is használható.

## <a name="install-required-software"></a>A szükséges szoftverek telepítése
Az Azure-beli virtuális hálózati átjáró több VPN-protokollt használó VPN-kapcsolatot is biztosít, beleértve az IPsec és az OpenVPN-t is. Ez az útmutató bemutatja, hogyan használható az IPsec, és hogyan használhatja a alapú strongswan csomagot a Linux-támogatás biztosításához. 

> Ellenőrizve Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Virtuális hálózat üzembe helyezése 
Ha egy pont – hely típusú VPN-kapcsolaton keresztül szeretné elérni az Azure-fájlmegosztást és más Azure-erőforrásokat, létre kell hoznia egy virtuális hálózatot vagy egy VNet. A P2S VPN-kapcsolat automatikusan létrejön a helyszíni Linux-gép és az Azure-beli virtuális hálózat között.

A következő szkript létrehoz egy három alhálózattal rendelkező Azure-beli virtuális hálózatot: egyet a Storage-fiók szolgáltatási végpontja számára, egyet a Storage-fiókja privát végpontja számára, amely a helyi Storage-fiókhoz való hozzáféréshez szükséges az egyéni létrehozása nélkül. a megváltoztatható Storage-fiók nyilvános IP-címének útválasztása, valamint egy a VPN-szolgáltatást biztosító virtuális hálózati átjáró. 

Ne felejtse el lecserélni `<region>`, `<resource-group>`és `<desired-vnet-name>` értéket a környezete megfelelő értékeivel.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>A Storage-fiók korlátozása a virtuális hálózatra
Alapértelmezés szerint a Storage-fiók létrehozásakor a világ bármely pontjáról elérheti azt, ha rendelkezik a kérés hitelesítéséhez szükséges eszközökkel (például a Active Directory identitásával vagy a Storage-fiók kulccsal). Ha korlátozni szeretné az ehhez a Storage-fiókhoz való hozzáférést az imént létrehozott virtuális hálózathoz, létre kell hoznia egy hálózati szabálykészlet, amely engedélyezi a hozzáférést a virtuális hálózaton belül, és megtagadja az összes többi hozzáférést.

A Storage-fiók virtuális hálózatra való korlátozásához szolgáltatási végpontot kell használni. A szolgáltatási végpont olyan hálózati szerkezet, amellyel a nyilvános DNS/nyilvános IP-cím csak a virtuális hálózatból érhető el. Mivel a nyilvános IP-cím nem garantált, hogy változatlan maradjon, végül privát végpontot szeretnénk használni a Storage-fiókhoz tartozó szolgáltatási végpont helyett, azonban nem lehet korlátozni a Storage-fiókot, kivéve, ha egy szolgáltatási végpont is elérhető.

Ne felejtse el lecserélni `<storage-account-name>` az elérni kívánt Storage-fiókkal.

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>Privát végpont létrehozása (előzetes verzió)
Ha létrehoz egy magánhálózati végpontot a Storage-fiókjához, a Storage-fiókja IP-címet ad a virtuális hálózat IP-címén belül. Ha az Azure-fájlmegosztást ezen magánhálózati IP-címmel csatlakoztatja a helyszíni környezetből, a VPN-telepítés által automatikusan definiált útválasztási szabályok a VPN-kapcsolaton keresztül irányítják a csatlakoztatási kérelmet a Storage-fiókhoz. 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>Tanúsítványok létrehozása a VPN-hitelesítéshez
Ahhoz, hogy a helyszíni linuxos gépek VPN-kapcsolatai hitelesítve legyenek a virtuális hálózathoz való hozzáféréshez, két tanúsítványt kell létrehoznia: egy főtanúsítványt, amelyet a rendszer a virtuálisgép-átjáró számára biztosít, valamint egy ügyféltanúsítványt, amely aláírva a főtanúsítvánnyal. A következő parancsfájl létrehozza a szükséges tanúsítványokat.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Virtuális hálózati átjáró üzembe helyezése
Az Azure-beli virtuális hálózati átjáró az a szolgáltatás, amellyel a helyszíni linuxos gépek csatlakozni fognak. A szolgáltatás üzembe helyezéséhez két alapvető összetevő szükséges: egy nyilvános IP-cím, amely azonosítja az átjárót az ügyfelek számára, bárhol is legyenek a világon, és egy korábban létrehozott főtanúsítvány, amelyet az ügyfelek hitelesítéséhez fog használni.

Ne felejtse el lecserélni a `<desired-vpn-name-here>`t az erőforrások nevével.

> [!Note]  
> Az Azure-beli virtuális hálózati átjáró üzembe helyezése akár 45 percet is igénybe vehet. Az erőforrás üzembe helyezése közben ez a bash parancsfájl-parancsfájl letiltja a központi telepítés befejeződését. Ez a várható eredmény.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>A VPN-ügyfél konfigurálása
Az Azure Virtual Network Gateway egy letölthető csomagot hoz létre a helyi linuxos gépen a VPN-kapcsolat inicializálásához szükséges konfigurációs fájlokkal. A következő parancsfájl elhelyezi a megfelelő helyen létrehozott tanúsítványokat, és konfigurálja a `ipsec.conf` fájlt a megfelelő értékekkel a letölthető csomagban található konfigurációs fájlból.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Azure-fájlmegosztás csatlakoztatása
Most, hogy beállította a pont – hely VPN-t, csatlakoztathatja az Azure-fájlmegosztást. A következő példa a megosztást nem állandóan csatlakoztatja. A folyamatos csatlakoztatáshoz tekintse meg az [Azure-fájlmegosztás használata Linux rendszeren](storage-how-to-use-files-linux.md)című témakört. 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Lásd még:
- [Azure Files hálózatkezelés – áttekintés](storage-files-networking-overview.md)
- [Pont – hely (P2S) VPN konfigurálása Windows rendszeren a Azure Files-mel való használatra](storage-files-configure-p2s-vpn-windows.md)
- [Helyek közötti (S2S) VPN konfigurálása Azure Fileshoz való használatra](storage-files-configure-s2s-vpn.md)