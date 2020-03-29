---
title: Csendes telepítés Az Azure AD App Proxy összekötő | Microsoft dokumentumok
description: Bemutatja, hogyan hajthatja végre az Azure AD alkalmazásproxy-összekötő felügyelet nélküli telepítését a helyszíni alkalmazások biztonságos távoli eléréséhez.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b43d2de0a366d7e69a025b2e4e2998dccda2038e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756211"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Felügyelet nélküli telepítési parancsfájl létrehozása az Azure AD alkalmazásproxy-összekötőhöz

Ez a témakör segít létrehozni egy Windows PowerShell-parancsfájlt, amely lehetővé teszi az Azure AD alkalmazásproxy-összekötő felügyelet nélküli telepítését és regisztrációját.

Ez a funkció akkor hasznos, ha a következőket szeretné:

* Telepítse az összekötőt olyan Windows-kiszolgálókra, amelyeken nincs engedélyezve a felhasználói felület, vagy amelyet a Távoli asztal lal nem tud elérni.
* Egyszerre több csatlakozót is bekell szerelnie és regisztrálnia.
* Integrálja az összekötő telepítését és regisztrációját egy másik eljárás részeként.
* Hozzon létre egy szabványos kiszolgálói lemezképet, amely tartalmazza az összekötő biteket, de nincs regisztrálva.

Ahhoz, hogy az [alkalmazásproxy-összekötő](application-proxy-connectors.md) működjön, regisztrálni kell az Azure AD-címtárban egy alkalmazás rendszergazda és jelszó használatával. Általában ezt az információt adja meg az összekötő telepítése során egy előugró párbeszédpanelen, de a PowerShell segítségével automatizálhatja ezt a folyamatot helyette.

A felügyelet nélküli telepítéshez két lépés áll. Először telepítse a csatlakozót. Másodszor regisztrálja az összekötőt az Azure AD-vel. 

## <a name="install-the-connector"></a>A csatlakozó telepítése
Az összekötő regisztrálás nélküli telepítéséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy parancssort.
2. Futtassa a következő parancsot, amelyben a /q csendes telepítést jelent. A csendes telepítés nem kéri a végfelhasználói licencszerződés elfogadását.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Az összekötő regisztrálása az Azure AD-vel
Az összekötő regisztrálásához két módszer használható:

* Az összekötő regisztrálása Windows PowerShell-hitelesítő objektum mal
* Az összekötő regisztrálása kapcsolat nélküli módban létrehozott jogkivonat használatával

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Az összekötő regisztrálása Windows PowerShell-hitelesítő objektum mal
1. Hozzon létre egy Windows `$cred` PowerShell-hitelesítő adatok objektumot, amely rendszergazdai felhasználónevet és jelszót tartalmaz a címtárhoz. Futtassa a következő parancsot a * \<felhasználónév\> * és * \<a jelszó lecserélésével:\>*
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Nyissa meg a **C:\Program Files\Microsoft AAD alkalmazásproxy-összekötő t,** és futtassa a következő parancsfájlt a `$cred` létrehozott objektummal:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Az összekötő regisztrálása kapcsolat nélküli módban létrehozott jogkivonat használatával
1. Hozzon létre egy offline jogkivonatot az AuthenticationContext osztály használatával az alábbi kódrészletben vagy az alábbi PowerShell-parancsmagokban szereplő értékek használatával:

    **C#használata:**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }

    **A PowerShell használata:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. Miután rendelkezik a jogkivonatkal, hozzon létre egy SecureString-et a jogkivonat használatával:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Futtassa a következő Windows \<PowerShell\> parancsot, és cserélje le a bérlői GUID azonosítót a címtárazonosítóra:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>További lépések 
* [Alkalmazások közzététele saját tartománynév használatával](application-proxy-configure-custom-domain.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)


