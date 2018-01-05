---
title: "Azure AD alkalmazás alkalmazásproxy-összekötő csendes telepítése |} Microsoft Docs"
description: "Bemutatja, hogyan adhat az Azure AD alkalmazásproxy-összekötő a helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak a felügyelet nélküli telepítést."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a89a64499fba5ad0adf55863d809857f00edb2d5
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Létrehozhat egy felügyelet nélküli telepítési parancsfájlt, az Azure AD alkalmazásproxy-összekötő

Ez a témakör segítséget nyújt a Windows PowerShell-parancsfájl, amely lehetővé teszi a felügyelet nélküli telepítése és regisztrálása az Azure AD alkalmazásproxy-összekötő létrehozásához.

Ez a funkció akkor hasznos, ha azt szeretné, hogy:

* Az összekötő telepítése a Windows Server kiszolgálókon, amely nem rendelkezik a felhasználói felület engedélyezve van, vagy a távoli asztal nem érhetők el.
* Telepíti és regisztrálja egyszerre sok összekötők.
* Integrálható a összekötő telepítése és regisztrálása egy másik művelet részeként.
* Hozzon létre egy szabványos kiszolgálói lemezképet, az összekötő bits tartalmaz, de nincs regisztrálva.

Az a [Application Proxy connector](application-proxy-understand-connectors.md) használatához regisztrálni kell az Azure AD-címtár globális rendszergazdája és jelszóval Önhöz. Általában ezt az információt is meg kell adni egy előugró párbeszédpanelen összekötő telepítése során, de a PowerShell használatával automatizálhatja inkább ezt a folyamatot.

A felügyelet nélküli telepítés két lépésben történik. Először telepítse az összekötőt. Másodszor az összekötő regisztrálására az Azure AD. 

## <a name="install-the-connector"></a>Az összekötő telepítése
Az összekötő regisztrálása nélkül telepítéséhez tegye a következőket:

1. Nyisson meg egy parancssort.
2. A következő parancsot, amelyben a /q Csendes telepítés azt jelenti. Csendes telepítés nem kéri a végfelhasználói licencszerződés elfogadásához.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Az összekötő regisztrálására az Azure AD
Az összekötő regisztrálása segítségével két módszer áll rendelkezésre:

* Egy Windows PowerShell hitelesítő objektumot használ a connector regisztrálása
* A létrehozott kapcsolat nélküli jogkivonat használatával connector regisztrálása

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Egy Windows PowerShell hitelesítő objektumot használ a connector regisztrálása
1. Hozzon létre egy Windows PowerShell hitelesítő objektumot `$cred` , amely tartalmazza egy rendszergazdai felhasználónév és jelszó a címtáron. A következő parancsot, cseréje  *\<felhasználónév\>*  és  *\<jelszó\>*:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Ugrás a **C:\Program Files\Microsoft AAD App alkalmazásproxy-összekötő** , és futtassa a következő parancsfájl a `$cred` létrehozott objektum:
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>A létrehozott kapcsolat nélküli jogkivonat használatával connector regisztrálása
1. Hozzon létre egy kapcsolat nélküli jogkivonat a következő kódrészletet az értékekkel AuthenticationContext osztály használatával:

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


2. Miután a jogkivonatot, hozzon létre egy SecureString a token használatával:

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Futtassa a következő Windows PowerShell-parancsot cseréje \<GUID bérlői\> a directory azonosítójú:

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>További lépések 
* [Alkalmazások közzététele saját tartománynév használatával](active-directory-application-proxy-custom-domains.md)
* [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md)
* [Az alkalmazásproxy problémák elhárítása](active-directory-application-proxy-troubleshoot.md)


