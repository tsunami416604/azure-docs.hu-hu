---
title: Always-On VPN-alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: Az Always On VPN-alagút VPN Gatewayhoz való konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: cherylmc
ms.openlocfilehash: 6f0c33ee7fd5790a060574230f1156c569a63936
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425670"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>AlwaysOn VPN-eszközalagút konfigurálása

A Windows 10 virtuális magánhálózati (VPN) ügyfél egyik új funkciója a VPN-kapcsolat fenntartásának lehetősége. Az Always on egy Windows 10 funkció, amely lehetővé teszi, hogy az aktív VPN-profil automatikusan kapcsolódjon, és az eseményindítók (azaz a felhasználói bejelentkezés, a hálózati állapot változása vagy az eszköz aktív állapota) alapján is csatlakoztatva maradjon.

Az Azure-beli virtuális hálózati átjárók a Windows 10 always on szolgáltatással használhatók állandó felhasználói alagutak, valamint az Azure-beli eszköz-alagutak létrehozására. Ez a cikk segítséget nyújt az Always ON VPN-eszközök bújtatásának konfigurálásához.

Az Always On VPN-kapcsolatok két típusú alagutat tartalmaznak:

* Az **eszköz-alagút** a megadott VPN-kiszolgálókhoz csatlakozik, mielőtt a felhasználók bejelentkeznek az eszközre. A Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszközkezelés célja az eszköz-alagút használata.

* A **felhasználói alagút** csak az eszköz felhasználói bejelentkezését követően csatlakozik. A felhasználói alagút lehetővé teszi a felhasználók számára a vállalati erőforrások elérését a VPN-kiszolgálókon keresztül.

Az eszköz-alagút és a felhasználói alagút egymástól függetlenül működik a VPN-profiljaival. Egyszerre csatlakozhatnak, és szükség szerint különböző hitelesítési módszereket és egyéb VPN-konfigurációs beállításokat használhatnak.

## <a name="1-configure-the-gateway"></a>1. az átjáró konfigurálása

Konfigurálja úgy a VPN-átjárót, hogy a IKEv2 és a tanúsítványalapú hitelesítést használja ezen [pont – hely cikk](vpn-gateway-howto-point-to-site-resource-manager-portal.md)alapján.

## <a name="2-configure-the-device-tunnel"></a>2. az eszköz bújtatásának konfigurálása

Az eszköz-alagút sikeres létrehozásához az alábbi követelményeknek kell teljesülniük:

* Az eszköznek olyan tartományhoz csatlakoztatott számítógépnek kell lennie, amely a Windows 10 Enterprise vagy az Education 1809-es vagy újabb verzióját futtatja.
* Az alagút csak a Windows beépített VPN-megoldásához állítható be, és a IKEv2 használatával a számítógép-Tanúsítványos hitelesítéssel van létrehozva. 
* Eszközönként csak egy eszköz-alagút állítható be.

1. Telepítse az Ügyféltanúsítványok szolgáltatást a Windows 10-es ügyfélen a [pont – hely VPN-ügyfél című cikkben](point-to-site-how-to-vpn-client-install-azure-cert.md)látható módon. A tanúsítványnak a helyi számítógép tárolójába kell esnie.
1. [Ezekkel az utasításokkal](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) hozhat létre VPN-profilt, és konfigurálhatja az eszköz-ALAGUTAT a helyi rendszerfiók környezetében.

### <a name="configuration-example-for-device-tunnel"></a>Az eszköz-alagút konfigurációs példája

Miután konfigurálta a virtuális hálózati átjárót, és telepítette az ügyféltanúsítványt a helyi számítógép tárolójába a Windows 10-ügyfélen, az alábbi példák segítségével konfigurálja az ügyfél-eszköz alagutat.

1. Másolja a következő szöveget, és mentse a ***devicecert. ps1***néven.

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
1. Másolja az alábbi szöveget, és mentse ***VPNProfile. XML*** néven a **devicecert. ps1**fájl mappájába. Szerkessze a következő szöveget a környezetének megfelelően.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

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
1. Töltse le a **PsExec** -t a [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) -ból, és bontsa ki a fájlokat a **C:\PSTools**.
1. A rendszergazda parancssorból indítsa el a PowerShellt a következő parancs futtatásával:

   ```
   PsExec.exe Powershell for 32-bit Windows
   PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. A PowerShellben váltson arra a mappára, ahol a **devicecert. ps1** és az **VPNProfile. xml fájl** található, és futtassa a következő parancsot:

   ```powershell
   .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Futtassa a **Rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Keresse meg a **MachineCertTest** bejegyzést, és kattintson a **kapcsolat**elemre.

   ![Kapcsolódás](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Ha a kapcsolatok sikeresek, indítsa újra a számítógépet. Az alagút automatikusan fog összekapcsolást.

## <a name="cleanup"></a>Felesleges tartalmak törlése

A profil eltávolításához futtassa a következő parancsot:

![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>Következő lépések

Hibaelhárítási információkért lásd: [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
