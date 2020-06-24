---
title: Csendes telepítés Azure AD alkalmazás proxy-összekötővel | Microsoft Docs
description: Az Azure AD Application Proxy-összekötő felügyelet nélküli telepítését ismerteti a helyszíni alkalmazások biztonságos távoli elérésének biztosításához.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/24/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b959649074e68d50a266f05841ce4c87e2b3e20
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760014"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Felügyelet nélküli telepítési parancsfájl létrehozása az Azure AD Application Proxy-összekötőhöz

Ebből a témakörből megtudhatja, hogyan hozhat létre felügyelet nélküli telepítést és regisztrációt lehetővé tevő Windows PowerShell-parancsfájlt az Azure AD Application Proxy-összekötőhöz.

Ez a funkció akkor hasznos, ha a következőket kívánja használni:

* Telepítse az összekötőt olyan Windows-kiszolgálókra, amelyeken nincs engedélyezve a felhasználói felület, vagy nem férhet hozzá Távoli asztalhoz.
* Egyszerre több összekötőt is telepíthet és regisztrálhat.
* Az összekötő telepítésének és regisztrálásának integrálása egy másik eljárás részeként.
* Hozzon létre egy szabványos kiszolgálói rendszerképet, amely tartalmazza az összekötő biteit, de nincs regisztrálva.

Az [alkalmazásproxy-összekötő](application-proxy-connectors.md) működéséhez regisztrálnia kell az Azure ad-címtárban az alkalmazás-rendszergazda és a jelszó használatával. Általában ez az információ az összekötő telepítésekor kerül be egy előugró párbeszédpanelen, de a PowerShell használatával automatizálhatja ezt a folyamatot.

A felügyelet nélküli telepítéshez két lépés szükséges. Először telepítse az összekötőt. Másodszor regisztrálja az összekötőt az Azure AD-ben.

## <a name="install-the-connector"></a>Az összekötő telepítése
A következő lépésekkel telepítheti az összekötőt a regisztráció nélkül:

1. Nyisson meg egy parancssort.
2. Futtassa a következő parancsot, amelyben a/q csendes telepítést jelent. A csendes telepítés nem kéri, hogy fogadja el a végfelhasználói licencszerződést.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Az összekötő regisztrálása az Azure AD-ben
Az összekötő regisztrálására két módszer használható:

* Az összekötő regisztrálása Windows PowerShell hitelesítőadat-objektum használatával
* Az összekötő regisztrálása offline kapcsolattal létrehozott jogkivonat használatával

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Az összekötő regisztrálása Windows PowerShell hitelesítőadat-objektum használatával
1. Hozzon létre egy Windows PowerShell hitelesítő adatokat `$cred` tartalmazó objektumot, amely tartalmazza a címtárhoz tartozó rendszergazdai felhasználónevet és jelszót. Futtassa a következő parancsot, és cserélje le *\<username\>* *\<password\>* :

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Nyissa meg a **C:\Program FILES\MICROSOFT HRE app proxy-összekötőt** , és futtassa a következő szkriptet a `$cred` létrehozott objektum használatával:

        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Az összekötő regisztrálása offline kapcsolattal létrehozott jogkivonat használatával
1. Hozzon létre egy offline tokent a AuthenticationContext osztály használatával az alábbi kódrészlet vagy PowerShell-parancsmagok értékeivel:

    **A C# használatával:**

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

2. Ha rendelkezik a jogkivonattal, hozzon létre egy SecureString a jogkivonat használatával:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Futtassa a következő Windows PowerShell-parancsot, \<tenant GUID\> és cserélje le a KÖNYVTÁRAT azonosítóra:

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>További lépések
* [Alkalmazások közzététele saját tartománynév használatával](application-proxy-configure-custom-domain.md)
* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)
