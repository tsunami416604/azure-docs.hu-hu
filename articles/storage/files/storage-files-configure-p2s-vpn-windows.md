---
title: Pont-hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files-szal való használatra | Microsoft dokumentumok
description: Pont-hely (P2S) VPN beállítása Windows rendszeren az Azure Files használatához
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273477"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Pont-hely (P2S) VPN konfigurálása Windows rendszeren az Azure Files használatával
A Point-to-Site (P2S) VPN-kapcsolat segítségével az Azure-fájlmegosztások csatlakoztatása SMB-en keresztül az Azure-on kívülről, a 445-ös port megnyitása nélkül. A pont-hely VPN-kapcsolat az Azure és az egyes ügyfelek közötti VPN-kapcsolat. P2S VPN-kapcsolat használatához az Azure Files, a P2S VPN-kapcsolat kell konfigurálni minden ügyfél, amely csatlakozni kíván. Ha sok ügyfél, amelynek csatlakoznia kell az Azure-fájlmegosztások a helyszíni hálózatról, használhatja a helyek közötti (S2S) VPN-kapcsolat helyett egy point-to-site kapcsolat minden ügyfél számára. További információ: A helyek közötti [VPN konfigurálása az Azure Files szolgáltatáshoz című témakörben olvashat.](storage-files-configure-s2s-vpn.md)

Azt javasoljuk, hogy olvassa el a hálózati szempontok közvetlen [Azure-fájlmegosztási hozzáférés,](storage-files-networking-overview.md) mielőtt folytatná ezt a cikket a teljes körű vita a hálózati lehetőségek az Azure Files.

A cikk részletezi a windowsos (Windows-ügyfél- és Windows-kiszolgálóalapú) VPN-kapcsolat konfigurálásának lépéseit az Azure-fájlmegosztások közvetlen helyszíni csatlakoztatásához. Ha az Azure File Sync forgalmat VPN-en keresztül szeretné irányítani, olvassa el [az Azure File Sync proxy és a tűzfal beállításainak konfigurálását.](storage-sync-files-firewall-and-proxy.md)

## <a name="prerequisites"></a>Előfeltételek
- Az Azure PowerShell-modul legújabb verziója. Az Azure PowerShell telepítéséről az [Azure PowerShell-modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps) és az operációs rendszer kiválasztása című témakörben talál további információt. Ha az Azure CLI-t windowsos használatra szeretné használni, azonban az alábbi utasítások megjelennek az Azure PowerShellszámára.

- Egy Azure-fájlmegosztást szeretne csatlakoztatni a helyszíni. Az Azure-fájlmegosztások a tárfiókokon belül vannak üzembe helyezve, amelyek olyan felügyeleti konstrukciók, amelyek egy megosztott tárolókészletet képviselnek, amelyben több fájlmegosztást, valamint más tárolási erőforrásokat, például blobtárolókat vagy várólistákat helyezhet üzembe. Az Azure-fájlmegosztások és tárfiókok üzembe helyezéséről az [Azure-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)című részben olvashat bővebben.

- A helyszíni csatlakoztatni kívánt Azure-fájlmegosztást tartalmazó tárfiók privát végpontja. Ha többet szeretne tudni a rról, hogyan hozhat létre privát [végpontot, olvassa el az Azure Files hálózati végpontok konfigurálása című témakört.](storage-files-networking-endpoints.md?tabs=azure-powershell) 

## <a name="deploy-a-virtual-network"></a>Virtuális hálózat telepítése
Az Azure-fájlmegosztás és más Azure-erőforrások elérése a helyszíni egy point-to-site VPN, létre kell hoznia egy virtuális hálózat, vagy virtuális hálózat. A P2S VPN-kapcsolat automatikusan létrehoz egy hidat a helyszíni Windows-gép és az Azure virtuális hálózat között.

A következő PowerShell létrehoz egy Azure virtuális hálózatot három alhálózattal: egyet a tárfiók szolgáltatásvégpontjához, egyet a tárfiók privát végpontjához, amely a helyszíni tárfiók eléréséhez szükséges anélkül, hogy egyéni útválasztást hozna létre a nyilvános IP-címhez, amely változhat, és egyet a vpn-szolgáltatást nyújtó virtuális hálózati átjáróhoz. 

Ne felejtse `<resource-group>`el `<desired-vnet-name>` kicserélni `<region>`a , és a környezetének megfelelő értékeket.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Főtanúsítvány létrehozása a VPN-hitelesítéshez
Ahhoz, hogy a helyszíni Windows-gépekRŐL származó VPN-kapcsolatok hitelesíthetők legyenek a virtuális hálózat eléréséhez, két tanúsítványt kell létrehoznia: egy legfelső szintű tanúsítványt, amelyet a virtuális gép átjárója kap meg, és egy ügyféltanúsítványt, amelyet a főtanúsítvánnyal alá kell írni. A következő PowerShell létrehozza a főtanúsítványt; az ügyféltanúsítvány az Azure virtuális hálózati átjáró létrehozása után jön létre az átjáróból származó információkkal. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Virtuális hálózati átjáró telepítése
Az Azure virtuális hálózati átjáró az a szolgáltatás, amelyhez a helyszíni Windows-gépek csatlakozni fog. A szolgáltatás üzembe helyezéséhez két alapvető összetevőre van szükség: egy nyilvános IP-cím, amely azonosítja az átjárót az ügyfelek számára, bárhol is legyenek a világon, és egy korábban létrehozott főtanúsítványt, amelyet az ügyfelek hitelesítésére használnak.

Ne felejtse el lecserélni `<desired-vpn-name-here>` az erőforrásokhoz kívánt nevet.

> [!Note]  
> Az Azure virtuális hálózati átjáró üzembe helyezése akár 45 percet is igénybe vehet. Az erőforrás üzembe helyezése közben ez a PowerShell-parancsfájl blokkolja a központi telepítés befejezéséhez. Ez a várható eredmény.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Ügyféltanúsítvány létrehozása
Az ügyféltanúsítvány a virtuális hálózati átjáró URI-jával jön létre. Ez a tanúsítvány a korábban létrehozott főtanúsítvánnyal van aláírva.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>A VPN-ügyfél konfigurálása
Az Azure virtuális hálózati átjáró létrehoz egy letölthető csomagot konfigurációs fájlokat szükséges inicializálása a VPN-kapcsolat a helyszíni Windows-gépen. A VPN-kapcsolatot a Windows 10/Windows Server 2016+ [Mindig VPN szolgáltatásával](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) fogjuk konfigurálni. Ez a csomag végrehajtható csomagokat is tartalmaz, amelyek szükség esetén konfigurálják az örökölt Windows VPN-ügyfelet. Ez az útmutató az örökölt Windows VPN-ügyfél helyett mindig vpn-alapú ügyfelet használ, mivel az Always On VPN-ügyfél lehetővé teszi a végfelhasználók számára, hogy rendszergazdai engedélyekkel ne csatlakozzanak/válasszanak le az Azure VPN-ről anélkül, hogy rendszergazdai engedélyekkel rendelkeznek a számítógépükhöz. 

A következő parancsfájl telepíti a virtuális hálózati átjárón való hitelesítéshez szükséges ügyféltanúsítványt, letölti és telepíti a VPN-csomagot. Ne feledje, hogy cserélje ki, `<computer1>` és `<computer2>` a kívánt számítógépeket. Ezt a parancsfájlt annyi gépen futtathatja, amennyire csak `$sessions` szeretné, ha további PowerShell-munkameneteket ad hozzá a tömbhöz. A használati fióknak minden ilyen gépen rendszergazdának kell lennie. Ha ezek közül a gépek közül az egyik az a helyi számítógép, amelyről a parancsfájlt futtatja, a parancsfájlt egy emelt szintű PowerShell-munkamenetből kell futtatnia. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Az Azure fájlmegosztásának csatlakoztatása
Most, hogy beállította a point-to-site VPN-t, használhatja az Azure-fájlmegosztás csatlakoztatására a PowerShellen keresztül beállított számítógépeken. A következő példa csatlakoztatja a megosztást, felsorolja a megosztás gyökérkönyvtárát, hogy bizonyítsa, hogy a megosztás valóban csatlakoztatva van, és a megosztás leválasztása. Sajnos nem lehetséges a megosztás tartós csatlakoztatása a PowerShell-távszószerint. Az állandó csatlakoztatáshoz olvassa el az [Azure-fájlmegosztás használata a Windows rendszerrel](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Lásd még
- [A közvetlen Azure-fájlmegosztási hozzáférés hálózati szempontjai](storage-files-networking-overview.md)
- [Pont-hely (P2S) VPN konfigurálása Linuxon az Azure Files-szal való használatra](storage-files-configure-p2s-vpn-linux.md)
- [Helyek közötti (S2S) VPN konfigurálása az Azure Files használatához](storage-files-configure-s2s-vpn.md)
