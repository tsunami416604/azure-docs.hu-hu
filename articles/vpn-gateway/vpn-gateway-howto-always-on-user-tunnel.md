---
title: Always On VPN-alagút konfigurálása VPN Gateway
description: Az Always On felhasználói VPN-alagút konfigurálásának lépései VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846471"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN felhasználói alagút konfigurálása

A Windows 10 virtuális magánhálózati (VPN) ügyfél egyik új funkciója a VPN-kapcsolat fenntartásának lehetősége. Az Always on egy Windows 10 funkció, amely lehetővé teszi, hogy az aktív VPN-profil automatikusan kapcsolódjon, és az eseményindítók (azaz a felhasználói bejelentkezés, a hálózati állapot változása vagy az eszköz aktív állapota) alapján is csatlakoztatva maradjon.

Az Azure-beli virtuális hálózati átjárók a Windows 10 always on szolgáltatással használhatók állandó felhasználói alagutak, valamint az Azure-beli eszköz-alagutak létrehozására. Ez a cikk segítséget nyújt az Always On VPN felhasználói alagút konfigurálásához.

Az Always On VPN-kapcsolatok két típusú alagutat tartalmaznak:

* Az **eszköz-alagút** a megadott VPN-kiszolgálókhoz csatlakozik, mielőtt a felhasználók bejelentkeznek az eszközre. A Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszközkezelés célja az eszköz-alagút használata.

* A **felhasználói alagút** csak az eszköz felhasználói bejelentkezését követően csatlakozik. A felhasználói alagút lehetővé teszi a felhasználók számára a vállalati erőforrások elérését a VPN-kiszolgálókon keresztül.

Az eszköz-alagút és a felhasználói alagút egymástól függetlenül működik a VPN-profiljaival. Egyszerre csatlakozhatnak, és szükség szerint különböző hitelesítési módszereket és egyéb VPN-konfigurációs beállításokat használhatnak.

## <a name="1-configure-the-gateway"></a>1. Az átjáró konfigurálása

Konfigurálja úgy a VPN-átjárót, hogy a IKEv2 és a tanúsítványalapú hitelesítést használja ezen [pont – hely cikk](vpn-gateway-howto-point-to-site-resource-manager-portal.md)alapján.

## <a name="2-configure-the-user-tunnel"></a>2. A felhasználói alagút konfigurálása

1. Telepítse az Ügyféltanúsítványok szolgáltatást a Windows 10-es ügyfélen a [pont – hely VPN-ügyfél című cikkben](point-to-site-how-to-vpn-client-install-azure-cert.md)látható módon. A tanúsítványnak az aktuális felhasználói tárolóban kell lennie
2. Konfigurálja az Always On VPN-ügyfelet a PowerShell, a SCCM vagy az Intune használatával az [alábbi utasítások](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)segítségével.

### <a name="configuration-example-for-user-tunnel"></a>Felhasználói alagút konfigurációs példája

Miután konfigurálta a virtuális hálózati átjárót, és telepítette az ügyféltanúsítványt a helyi számítógép tárolójába a Windows 10-ügyfélen, az alábbi példák segítségével konfigurálja az ügyfél-eszköz alagutat.

1. Másolja a következő szöveget, és mentse a ***usercert. ps1***néven.

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
1. Másolja az alábbi szöveget, és mentse ***VPNProfile. XML*** néven a **usercert. ps1**fájl mappájába. Szerkessze a következő szöveget a környezetének megfelelően.

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. Futtassa a PowerShellt rendszergazdaként.

1. A PowerShellben váltson arra a mappára, ahol a **usercert. ps1** és az **VPNProfile. xml fájl** található, és futtassa a következő parancsot:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. Keresse meg a VPN-beállítások menüpontot.

1. Keresse meg a **UserTest** bejegyzést, és kattintson a **kapcsolat**elemre.

1. Ha a kapcsolatok sikeresek, akkor sikeresen konfigurált egy mindig felhasználói alagutat.

## <a name="cleanup"></a>Felesleges tartalmak törlése

A profil eltávolításához futtassa a következő parancsot:

1. Válassza le a kapcsolatot, és törölje a jelet a "csatlakozás automatikusan"

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>További lépések

Hibaelhárítási információkért lásd: [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
