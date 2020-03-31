---
title: Pont-hely (P2S) VPN konfigurálása Linuxon az Azure Files-szal való használatra | Microsoft dokumentumok
description: Pont-hely (P2S) VPN konfigurálása Linuxon az Azure Files használatához
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cfff05ed52258ee448d83a521b99dca7d356a0f9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061052"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Pont-hely (P2S) VPN konfigurálása Linuxon az Azure Files-szal való használatra
A Point-to-Site (P2S) VPN-kapcsolat segítségével az Azure-fájlmegosztások csatlakoztatása SMB-en keresztül az Azure-on kívülről, a 445-ös port megnyitása nélkül. A pont-hely VPN-kapcsolat az Azure és az egyes ügyfelek közötti VPN-kapcsolat. P2S VPN-kapcsolat használatához az Azure Files, a P2S VPN-kapcsolat kell konfigurálni minden ügyfél, amely csatlakozni kíván. Ha sok ügyfél, amelynek csatlakoznia kell az Azure-fájlmegosztások a helyszíni hálózatról, használhatja a helyek közötti (S2S) VPN-kapcsolat helyett egy point-to-site kapcsolat minden ügyfél számára. További információ: A helyek közötti [VPN konfigurálása az Azure Files szolgáltatáshoz című témakörben olvashat.](storage-files-configure-s2s-vpn.md)

Azt javasoljuk, hogy olvassa el [az Azure Files hálózati áttekintést,](storage-files-networking-overview.md) mielőtt folytatná ezt a cikket az Azure Files elérhető hálózati lehetőségek teljes körű megvitatására.

A cikk ismerteti a lépéseket, hogy konfigurálja a Point-to-Site VPN Linuxon az Azure-fájlmegosztások közvetlenül a helyszínen csatlakoztatása. Ha az Azure File Sync forgalmat VPN-en keresztül szeretné irányítani, olvassa el [az Azure File Sync proxy és a tűzfal beállításainak konfigurálását.](storage-sync-files-firewall-and-proxy.md)

## <a name="prerequisites"></a>Előfeltételek
- Az Azure CLI legújabb verziója. Az Azure CLI telepítéséről az [Azure PowerShell CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és az operációs rendszer kiválasztása című témakörben talál további információt. Ha az Azure PowerShell-modult Linuxon szeretné használni, azonban az alábbi utasítások az Azure CLI-hez kerülnek bemutatásra.

- Egy Azure-fájlmegosztást szeretne csatlakoztatni a helyszíni. Az Azure-fájlmegosztások a tárfiókokon belül vannak üzembe helyezve, amelyek olyan felügyeleti konstrukciók, amelyek egy megosztott tárolókészletet képviselnek, amelyben több fájlmegosztást, valamint más tárolási erőforrásokat, például blobtárolókat vagy várólistákat helyezhet üzembe. Az Azure-fájlmegosztások és tárfiókok üzembe helyezéséről az [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című részben olvashat bővebben.

- A helyszíni csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiók privát végpontja. Ha többet szeretne tudni a rról, hogyan hozhat létre privát [végpontot, olvassa el az Azure Files hálózati végpontok konfigurálása című témakört.](storage-files-networking-endpoints.md?tabs=azure-cli) 

## <a name="install-required-software"></a>A szükséges szoftverek telepítése
Az Azure virtuális hálózati átjáró több VPN-protokoll használatával biztosít VPN-kapcsolatokat, beleértve az IPsec-et és az OpenVPN-t. Ez az útmutató bemutatja, hogyan kell használni az IPsec-et, és a strongSwan csomagot használja a Linux támogatásához. 

> Ellenőrzött Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Virtuális hálózat telepítése 
Az Azure-fájlmegosztás és más Azure-erőforrások elérése a helyszíni egy point-to-site VPN, létre kell hoznia egy virtuális hálózat, vagy virtuális hálózat. A P2S VPN-kapcsolat automatikusan létrehoz egy hidat a helyszíni Linux-gép és az Azure virtuális hálózat között.

A következő parancsfájl három alhálózattal hoz létre egy Azure virtuális hálózatot: egyet a tárfiók szolgáltatásvégpontjához, egyet a tárfiók privát végpontjához, amely a helyszíni tárfiók eléréséhez szükséges egyéni a nyilvános IP-cím hez, amely változhat, és egyet a VPN-szolgáltatást nyújtó virtuális hálózati átjáróhoz. 

Ne felejtse `<resource-group>`el `<desired-vnet-name>` kicserélni `<region>`a , és a környezetének megfelelő értékeket.

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

## <a name="create-certificates-for-vpn-authentication"></a>Tanúsítványok létrehozása vpn-hitelesítéshez
Ahhoz, hogy a helyszíni Linux-gépekVPN-kapcsolatait hitelesíteni lehessen a virtuális hálózat eléréséhez, két tanúsítványt kell létrehoznia: egy főtanúsítványt, amelyet a virtuális gép isztorának biztosítanak, és egy ügyféltanúsítványt, amely a főtanúsítvánnyal. A következő parancsfájl létrehozza a szükséges tanúsítványokat.

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

## <a name="deploy-virtual-network-gateway"></a>Virtuális hálózati átjáró telepítése
Az Azure virtuális hálózati átjáró az a szolgáltatás, amelyhez a helyszíni Linux-gépek csatlakozni fog. A szolgáltatás üzembe helyezéséhez két alapvető összetevőre van szükség: egy nyilvános IP-cím, amely azonosítja az átjárót az ügyfelek számára, bárhol is legyenek a világon, és egy korábban létrehozott főtanúsítványt, amely et az ügyfelek hitelesítésére fog használni.

Ne felejtse el lecserélni `<desired-vpn-name-here>` az erőforrásokhoz kívánt nevet.

> [!Note]  
> Az Azure virtuális hálózati átjáró üzembe helyezése akár 45 percet is igénybe vehet. Az erőforrás telepítése közben ez a bash parancsfájl blokkolja a központi telepítés befejezéséhez. Ez a várható eredmény.

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
Az Azure virtuális hálózati átjáró létrehoz egy letölthető csomagot konfigurációs fájlokat szükséges inicializálása a VPN-kapcsolat a helyszíni Linux-gép. A következő parancsfájl a megfelelő helyre helyezi a `ipsec.conf` létrehozott tanúsítványokat, és a fájlt a megfelelő értékekkel konfigurálja a letölthető csomagkonfigurációs fájlból.

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

## <a name="mount-azure-file-share"></a>Az Azure fájlmegosztásának csatlakoztatása
Most, hogy beállította a pont-hely VPN-t, csatlakoztathatja az Azure-fájlmegosztást. A következő példa nem tartósan csatlakoztatja a megosztást. Az állandó csatlakoztatáshoz [lásd: Azure-fájlmegosztás használata Linux mal.](storage-how-to-use-files-linux.md) 

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

## <a name="see-also"></a>Lásd még
- [Az Azure Files hálózati áttekintése](storage-files-networking-overview.md)
- [Pont-hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files használatával](storage-files-configure-p2s-vpn-windows.md)
- [Helyek közötti (S2S) VPN konfigurálása az Azure Files használatához](storage-files-configure-s2s-vpn.md)