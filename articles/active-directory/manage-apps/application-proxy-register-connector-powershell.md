---
title: Beavatkozás nélküli telepítés az Azure AD-alkalmazásproxy-összekötő |} A Microsoft Docs
description: A cikk ismerteti, hogyan hajtsa végre az Azure AD alkalmazásproxy-összekötő a helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak a felügyelet nélküli telepítéséhez.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 2b0d81a6afb754f03f49b72592f4e04106d2a088
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467953"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Hozzon létre egy felügyelet nélküli telepítési parancsfájlt, az Azure AD-alkalmazásproxy-összekötő

Ez a témakör segít a Windows PowerShell-parancsfájl, amely lehetővé teszi a felügyelet nélküli telepítési és regisztrációs az Azure AD-alkalmazásproxy-összekötő létrehozásához.

Ez a funkció akkor hasznos, ha meg szeretné:

* Telepítse a Windows-kiszolgálók, amely nem rendelkezik a felhasználói felület engedélyezve van, vagy a távoli asztal nem érhetők el.
* Telepíti és regisztrálja a számos összekötő egyszerre.
* Az összekötő telepítése és a egy másik eljárás részeként regisztrációs integrálhatja.
* Hozzon létre egy szabványos server-lemezképet, az összekötő bits tartalmaz, de nincs regisztrálva.

Az a [Application Proxy connector](application-proxy-connectors.md) regisztrálni kell az Azure AD-címtár globális rendszergazdája és jelszóval rendelkezik használatát. Rendszerint ezeket az információkat is meg kell adni egy felugró párbeszédpanel összekötő telepítése során, de a PowerShell használatával automatizálható ehelyett Ez a folyamat.

Felügyelet nélküli telepítéséhez két lépésből áll. Először telepítse az összekötőt. Másodszor az összekötő regisztrálására az Azure ad-ben. 

## <a name="install-the-connector"></a>Az összekötő telepítése
A következő lépések segítségével telepítse az összekötőt, akkor regisztrálás nélkül:

1. Nyisson meg egy parancssort.
2. Futtassa a következő parancsot, amelyben a /q azt jelenti, hogy csendes telepítést. A csendes telepítést nem kérni fogja, hogy a végfelhasználói licencszerződés elfogadásához.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Az összekötő regisztrálására az Azure ad-vel
Az összekötő regisztrálására használható két módszer van:

* A Windows PowerShell hitelesítő objektumot használ az összekötő regisztrálása
* A létrehozott kapcsolat nélküli tokennel összekötő regisztrálása

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>A Windows PowerShell hitelesítő objektumot használ az összekötő regisztrálása
1. Hozzon létre egy Windows PowerShell hitelesítő objektumot `$cred` , amely tartalmazza a megfelelő rendszergazdai felhasználónév és jelszó a címtár számára. Futtassa a következő parancsot, és cserélje le *\<felhasználónév\>* és  *\<jelszó\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Lépjen a **C:\Program Files\Microsoft AAD-alkalmazás alkalmazásproxy-összekötő** , és futtassa az alábbi szkriptet az a `$cred` létrehozott objektum:
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>A létrehozott kapcsolat nélküli tokennel összekötő regisztrálása
1. Ez a kódrészlet vagy az alábbi PowerShell-parancsmagok az értékek AuthenticationContext osztállyal offline jogkivonat létrehozása:

    **C# használatával:**

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

    **PowerShell-lel:**

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

2. Miután a jogkivonatot, hozzon létre egy SecureString a jogkivonat használatával:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Futtassa a következő Windows PowerShell-parancsot, és cserélje le \<GUID bérlői\> a címtár-azonosító:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>További lépések 
* [Alkalmazások közzététele saját tartománynév használatával](application-proxy-configure-custom-domain.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Problémák merültek fel az alkalmazásproxy használatával](application-proxy-troubleshoot.md)


