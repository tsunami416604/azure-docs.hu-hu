---
title: Egy mindig bekapcsolt VPN-alagutat VPN-átjáró konfigurálása
description: VPN Gateway átjáró esetében mindig bekapcsolt VPN-alagút konfigurálásának lépései
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptional
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 81822297dcf9370fc8ce7f7ce0285689c31606ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695797"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>AlwaysOn VPN-eszközalagút konfigurálása

Az új szolgáltatások a Windows 10-es virtuális magánhálózati (VPN) ügyfél egyik, a VPN-kapcsolat fenntartása. Always On funkciója a Windows 10-es, amely lehetővé teszi az aktív VPN-profil automatikus csatlakozás és csatlakoztatott eseményindítók alapján – azaz felhasználói bejelentkezés, hálózati állapot módosítása vagy aktív eszköz képernyőjén.

Az Azure virtuális hálózati átjárók állandó felhasználói alagút, valamint az eszköz alagutak az Azure-bA létrehozására használható a Windows 10-es Always On. Ez a cikk segítséget nyújt egy Always ON VPN-eszköz alagút konfigurálása.

Mindig bekapcsolva beállítású VPN-kapcsolatok kétféle alagút tartalmaznak:

* **Eszköz alagút** csatlakozik a megadott VPN-kiszolgálókhoz, mielőtt bejelentkeznek az eszköz. Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszköz felügyeleti célokra használja az eszköz alagutat.

* **Felhasználói alagút** csak az eszköz egy felhasználó bejelentkezése után kapcsolódik. Felhasználói alagút lehetővé teszi, hogy a felhasználók számára a VPN-kiszolgálókon keresztül férnek hozzá a szervezeti erőforrásokhoz.

Eszköz alagút és alagút felhasználói is egymástól függetlenül a saját VPN-profilok működnek. Csatlakozhat egy időben, és különböző hitelesítési módszerek és a VPN-konfigurációs beállításait is használja, szükség szerint.

## <a name="1-configure-the-gateway"></a>1. Az átjáró konfigurálása

Az IKEv2 és Tanúsítványalapú hitelesítés használatával a VPN-átjáró konfigurálása [pont – hely cikk](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="2-configure-the-user-tunnel"></a>2. A felhasználó bújtatás konfigurálása

1. Ügyféltanúsítványok telepítése a Windows 10-es ügyfél, ahogy ez az [pont – hely VPN-ügyfél cikk](point-to-site-how-to-vpn-client-install-azure-cert.md). A tanúsítványt kell lennie a jelenlegi felhasználó Store
2. A PowerShell, az SCCM vagy az Intune használatával mindig bekapcsolva beállítású VPN-ügyfél konfigurálása [ezek az utasítások](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections).

## <a name="3-configure-the-device-tunnel"></a>3. Az eszköz alagút konfigurálása

Az alábbi követelményeknek teljesülniük kell ahhoz, hogy egy eszköz alagút sikeres létesítéséhez:

* Az eszköz egy Windows 10 Enterprise és Education verzióját futtató 1709-es vagy újabb verzióját a tartományhoz csatlakozó számítógépen kell lennie.
* Az alagút csak konfigurálható a Windows beépített VPN-megoldás, és az IKEv2 számítógép-tanúsítvány hitelesítése a létrejött. 
* Csak egy eszköz alagút eszközönként konfigurálható.

1. Ügyféltanúsítványok telepítése a Windows 10-es ügyfél, ahogy ez az [pont – hely VPN-ügyfél cikk](point-to-site-how-to-vpn-client-install-azure-cert.md). A tanúsítványt kell lennie a helyi számítógép tárolójában.
1. Használat [ezek az utasítások](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration) VPN-profil létrehozása és konfigurálása eszköz alagutat a helyi rendszerfiók környezetében.

### <a name="configuration-example-for-device-tunnel"></a>Példa konfigurációs eszköz alagút

Miután beállította a virtuális hálózati átjáró és a helyi számítógép tárolójában, a Windows 10-es ügyfél telepítve van az ügyféltanúsítvány, használja az alábbi példák egy ügyfél eszköz alagút konfigurálásához.

1. Másolja az alábbi szöveget, és mentse ***devicecert.ps1***.

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
1. Másolja az alábbi szöveget, és mentse ***VPNProfile.xml*** ugyanabban a mappában **devicecert.ps1**. Szerkessze a következő szöveget a környezetéhez.

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
1. Töltse le **PsExec** a [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) , és bontsa ki a fájlokat **C:\PSTools**.
1. Az egy rendszergazdai parancssort indítsa el a Powershellt futtatásával:

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![PowerShell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. A PowerShellben váltson arra a mappára, ahol **devicecert.ps1** és **VPNProfile.xml** találhatók, és futtassa a következő parancsot:

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. Futtatás **rasphone**.

   ![Rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. Keresse meg a **MachineCertTest** bejegyzést, és kattintson **Connect**.

   ![Kapcsolódás](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. Ha a kapcsolódás sikeres, indítsa újra a számítógépet. Az alagút automatikusan kapcsolódik.

## <a name="cleanup"></a>Felesleges tartalmak törlése

A profil eltávolításához futtassa a következő parancsot:

![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>További lépések

Című témakörben [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
