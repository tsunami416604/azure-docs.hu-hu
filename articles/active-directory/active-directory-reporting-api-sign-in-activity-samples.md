---
title: Azure Active Directory bejelentkezési tevékenység jelentés API-minták |} Microsoft Docs
description: Útmutató az Azure Active Directory Reporting API használatába
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: c41c1489-726b-4d3f-81d6-83beb932df9c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 466755d7d1cc7fbf4006826ac849b74ba306bae9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698577"
---
# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory bejelentkezési tevékenység jelentés API-minták
Ez a cikk az Azure Active Directory reporting API-val kapcsolatos cikkek gyűjteménye részét képezi.  
Az Azure AD jelentéskészítési lehetőséget biztosít az API-k, amely lehetővé teszi a kód vagy a kapcsolódó eszközök bejelentkezési tevékenység adatok eléréséhez.  
Ez a cikk a hatóköre használatával látják el a mintakódot az **bejelentkezés tevékenység API**.

Lásd:

* [A naplók](active-directory-reporting-azure-portal.md#activity-reports) további információ
* [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md) a reporting API-val kapcsolatos további információk.


## <a name="prerequisites"></a>Előfeltételek
A mintákat a cikkben használata előtt végre kell hajtania a [Előfeltételek az Azure AD reporting API eléréséhez](active-directory-reporting-api-prerequisites.md).  

## <a name="powershell-script"></a>PowerShell-szkript

```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfSignInDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/signIns"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into SignIns$i.json"
        if($docCount -le $countOfSignInDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath SignIns$i.json -append  -Force       
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```




## <a name="executing-the-script"></a>A parancsfájl végrehajtása
Miután a parancsfájl szerkesztése, futtassa, és győződjön meg arról, hogy a bejelentkezési a várt adatokat naplózza a jelentés adja vissza.

A parancsfájl kimenete a bejelentkezési jelentés JSON formátumban ad vissza. Emellett létrehoz egy `SignIns.json` az azonos kimenethez fájlt. Ha adatokat más jelentések, és a kimeneti formátum, amely nem kell megjegyzésbe a parancsfájl módosításával kísérletezhet.

## <a name="next-steps"></a>További lépések
* Szeretné testre szabhatja a mintákat a cikkben? Tekintse meg a [Azure Active Directory bejelentkezési tevékenység API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin). 
* Ha azt szeretné, hogy teljes áttekintése, az Azure Active Directory reporting API használatával [Bevezetés az Azure Active Directory reporting API használatába](active-directory-reporting-api-getting-started.md).
* Ha meg szeretne többet megtudni az Azure Active Directory reporting, tekintse meg a [Azure Active Directory-jelentéskészítés – útmutató](active-directory-reporting-guide.md).  

