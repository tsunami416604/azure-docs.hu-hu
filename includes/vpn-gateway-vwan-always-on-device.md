---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: feaf72de1d2c578d2b2d0df9e86ec0fbe0b49445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371742"
---
Az eszközalagút sikeres létrehozásához a következő követelményeknek kell megfelelni:

* Az eszköznek windows 10 Enterprise vagy Education 1809-es vagy újabb verzióját futtató tartományhoz csatlakozó számítógépnek kell lennie.
* Az alagút csak a Windows beépített VPN-megoldásához konfigurálható, és az IKEv2 használatával jön létre a számítógép tanúsítványának hitelesítésével.
* Eszközönként csak egy eszközalagút konfigurálható.

1. Telepítse az ügyféltanúsítványokat a Windows 10-ügyfélre a [pont-hely VPN ügyfélcikk](../articles/vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md) használatával. A tanúsítványnak a Helyi számítógép tárolóban kell lennie.
1. Hozzon létre egy VPN-profilt, és konfigurálja az eszközbújtatást a LOCAL SYSTEM fiók környezetében [az alábbi utasítások segítségével.](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)

### <a name="configuration-example-for-device-tunnel"></a>Konfigurációs példa az eszközalagúthoz

Miután konfigurálta a virtuális hálózati átjárót, és telepítette az ügyféltanúsítványt a Windows 10-ügyfél helyi számítógép tárolójában, az alábbi példák segítségével konfigurálhat ügyféleszköz-bújtatást:

1. Másolja a következő szöveget, és mentse ***devicecert.ps1***néven.

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. Másolja a következő szöveget ***vpnProfile.xml*** fájlba a **devicecert.ps1**fájllal azonos mappába. A környezetnek megfelelően szerkesztheti a következő szöveget.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers> <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address> <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address> <= IP of resource in the vnet or the vnet address space`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. Töltse le a **PsExec-et** a [Sysinternals-ból, és bontsa](https://docs.microsoft.com/sysinternals/downloads/psexec) ki a fájlokat a **C:\PSTools mappába.**
1. Egy rendszergazdai CMD-parancssorból indítsa el a PowerShellt a következő futtatásával:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-vwan-always-on-device/powershell.png)
1. A PowerShellben váltson arra a mappára, amelyben a **devicecert.ps1** és a **VPNProfile.xml** fájl található, és futtassa a következő parancsot:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-vwan-always-on-device/machinecerttest.png)
1. Fuss **rasphone**.

   ![rasphone között](./media/vpn-gateway-vwan-always-on-device/rasphone.png)
1. Keresse meg a **MachineCertTest bejegyzést,** és kattintson a **Csatlakozás gombra.**

   ![Kapcsolódás](./media/vpn-gateway-vwan-always-on-device/connect.png)
1. Ha a kapcsolat sikeres, indítsa újra a számítógépet. Az alagút automatikusan csatlakozik.
