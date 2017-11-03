---
title: "Azure Active Directory bejelentkezési tevékenység jelentés API-minták |} Microsoft Docs"
description: "Útmutató az Azure Active Directory Reporting API használatába"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: d8fec08deb44ec1cbb58f45ff5a01d348d99945a
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory bejelentkezési tevékenység jelentés API-minták
Ez a témakör az Azure Active Directory reporting API-val kapcsolatos témakörök gyűjteményét részét képezi.  
Az Azure AD jelentéskészítési lehetőséget biztosít az API-k, amely lehetővé teszi a kód vagy a kapcsolódó eszközök bejelentkezési tevékenység adatok eléréséhez.  
Ez a témakör a hatóköre használatával látják el a mintakódot az **bejelentkezés tevékenység API**.

Lásd:

* [A naplók](active-directory-reporting-azure-portal.md#activity-reports) további információ
* [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md) a reporting API-val kapcsolatos további információk.


## <a name="prerequisites"></a>Előfeltételek
A minták ebben a témakörben használata előtt végre kell hajtania a [Előfeltételek az Azure AD reporting API eléréséhez](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>PowerShell-szkript
    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.microsoftonline.com/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"

    $i=0

    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {

        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>A parancsfájl végrehajtása
Miután a parancsfájl szerkesztése, futtassa, és győződjön meg arról, hogy a naplózás a várt adatokat naplózza a jelentés adja vissza.

A parancsfájl kimenete a bejelentkezési jelentés JSON formátumban ad vissza. Emellett létrehoz egy `SigninActivities.json` az azonos kimenethez fájlt. Ha adatokat más jelentések, és a kimeneti formátum, amely nem kell megjegyzésbe a parancsfájl módosításával kísérletezhet.

## <a name="next-steps"></a>Következő lépések
* Szeretné testre szabhatja a minták ebben a témakörben? Tekintse meg a [Azure Active Directory bejelentkezési tevékenység API-referencia](active-directory-reporting-api-sign-in-activity-reference.md). 
* Ha azt szeretné, hogy teljes áttekintése, az Azure Active Directory reporting API használatával [Bevezetés az Azure Active Directory reporting API használatába](active-directory-reporting-api-getting-started.md).
* Ha meg szeretne többet megtudni az Azure Active Directory reporting, tekintse meg a [Azure Active Directory-jelentéskészítés – útmutató](active-directory-reporting-guide.md).  

