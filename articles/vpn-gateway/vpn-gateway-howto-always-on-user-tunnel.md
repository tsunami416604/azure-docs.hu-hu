---
title: Always-On VPN felhasználói alagút konfigurálása
titleSuffix: Azure VPN Gateway
description: Ez a cikk bemutatja, hogyan konfigurálhat egy always on VPN-felhasználói alagutat a VPN-átjáróhoz
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: a22ba5d8b33dd41fcc76c65fcddaf60c1c0ed5e3
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514748"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>AlwaysOn VPN felhasználói alagút konfigurálása

A Windows 10 VPN-ügyfél új funkciója, a always on, a VPN-kapcsolat fenntartásának lehetősége. A always on használatával az aktív VPN-profil automatikusan csatlakozhat, és az eseményindítók alapján csatlakoztatva maradhat, például a felhasználói bejelentkezés, a hálózati állapot módosítása vagy az eszközbeállítások aktív állapota.

Az Azure-beli virtuális hálózati átjárók és a Windows 10 always on használatával állandó felhasználói alagutak és eszköz-alagutak hozhatók létre az Azure-ba. Ez a cikk segítséget nyújt az Always On VPN felhasználói alagút konfigurálásához.

Az Always On VPN-kapcsolatok a következő két típusú alagutat tartalmazzák:

* **Eszköz-alagút**: csatlakozás a megadott VPN-kiszolgálókhoz, mielőtt a felhasználók bejelentkeznek az eszközre. A Bejelentkezés előtti kapcsolódási forgatókönyvek és az eszközkezelés egy eszköz-alagutat használnak.

* **Felhasználói alagút**: csak azt követően csatlakozik, hogy a felhasználók bejelentkeznek az eszközre. A felhasználói alagutak használatával VPN-kiszolgálókon keresztül érheti el a szervezeti erőforrásokat.

Az eszköz-alagutak és a felhasználói alagutak a VPN-profiljaik függetlenül működnek. Egyszerre csatlakozhatnak egymáshoz, és szükség szerint különböző hitelesítési módszereket és egyéb VPN-konfigurációs beállításokat használhatnak.

A következő fejezetekben egy VPN-átjárót és egy felhasználói alagutat konfigurál.

## <a name="step-1-configure-a-vpn-gateway"></a>1\. lépés: a VPN-átjáró konfigurálása

A VPN-átjárót úgy konfigurálja, hogy a IKEv2 és tanúsítványalapú hitelesítést használja az ebben a [pont – hely](vpn-gateway-howto-point-to-site-resource-manager-portal.md) cikkben található utasításokat követve.

## <a name="step-2-configure-a-user-tunnel"></a>2\. lépés: felhasználói alagút konfigurálása

1. Telepítse az Ügyféltanúsítványok szolgáltatást a Windows 10-es ügyfélre, ahogyan az ebben a [pont – hely VPN-ügyfél](point-to-site-how-to-vpn-client-install-azure-cert.md) című cikkben látható. A tanúsítványnak az aktuális felhasználói tárolóban kell lennie.

1. Konfigurálja az Always On VPN-ügyfelet a PowerShell, a Configuration Manager vagy az Intune használatával a [Windows 10-ügyfél konfigurálása mindig a VPN-kapcsolatokon](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)című témakör útmutatását követve.

### <a name="example-configuration-for-the-user-tunnel"></a>Példa a felhasználói alagút konfigurációjának használatára

Miután konfigurálta a virtuális hálózati átjárót, és telepítette az ügyféltanúsítványt a Windows 10-es ügyfél helyi számítógép tárolójába, konfigurálja az ügyfél-eszköz alagutat az alábbi példák használatával:

1. Másolja a következő szöveget, és mentse a *usercert. ps1*néven:

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
1. Másolja az alábbi szöveget, és mentse *VPNProfile. XML* néven a *usercert. ps1*fájl mappájába. Szerkessze a következő szöveget a környezetének megfelelően:

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  <= Can be found in the VpnSettings.xml in the downloaded profile zip file`
   * `<Address>192.168.3.5</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<Address>192.168.3.4</Address>  <= IP of resource in the vnet or the vnet address space`
   * `<PrefixSize>32</PrefixSize>     <= Subnet mask`

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

1. A PowerShellben váltson arra a mappára, ahol a *usercert. ps1* és az *VPNProfile. xml fájl* található, és futtassa a következő parancsot:

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. A **VPN-beállítások**területen keresse meg a **UserTest** bejegyzést, majd válassza a **Csatlakozás**lehetőséget.

1. Ha a kapcsolatok sikeresek, sikeresen konfigurált egy mindig felhasználói alagutat.

## <a name="clean-up-your-resources"></a>Az erőforrások törlése

A profil eltávolításához tegye a következőket:

1. Futtassa az alábbi parancsot:

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. Válassza le a kapcsolatot, és törölje az **Automatikus csatlakozás** jelölőnégyzet jelölését.

![Felesleges tartalmak törlése](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>Következő lépések

Az esetlegesen előforduló kapcsolódási problémák elhárításához tekintse meg az [Azure pont – hely kapcsolati problémák](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)című témakört.
