---
title: Az Azure Active Directory reporting API minták naplózása |} Microsoft Docs
description: Útmutató az Azure Active Directory Reporting API használatába
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/30/2018
ms.author: dhanyahk;rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 77ecb1f0c4b3614c9692715edae21a09f261b277
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34588164"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Az Azure Active Directory reporting API naplózási minták
Ez a cikk az Azure Active Directory reporting API-val kapcsolatos cikkek gyűjteménye részét képezi.  
Az Azure AD jelentéskészítési lehetőséget biztosít az API-k, amely lehetővé teszi a kód vagy a kapcsolódó eszközök naplózási adatok eléréséhez.
Ez a cikk a hatóköre használatával látják el a mintakódot az **API naplózási**.

Lásd:

* [A naplók](active-directory-reporting-azure-portal.md#activity-reports) további információ
* [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md) a reporting API-val kapcsolatos további információk.

Kérdéseit, vagy visszajelzést, lépjen kapcsolatba [AAD jelentéskészítési súgó](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Előfeltételek
A mintákat a cikkben használata előtt végre kell hajtania a [Előfeltételek az Azure AD reporting API eléréséhez](active-directory-reporting-api-prerequisites.md).  

## <a name="known-issue"></a>Ismert hiba
Alkalmazáshitelesítési nem működik, ha a bérlő az EU-régióban van. Használjon felhasználói hitelesítési megoldás a naplózási API eléréséhez, amíg azt hárítsa el a problémát. 

## <a name="powershell-script"></a>PowerShell-szkript


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
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

### <a name="executing-the-powershell-script"></a>A PowerShell-parancsprogram végrehajtása
Miután a parancsfájl szerkesztése, futtassa, és győződjön meg arról, hogy a naplózás a várt adatokat naplózza a jelentés adja vissza.

A parancsfájl kimenete a naplózási jelentés JSON formátumban ad vissza. Emellett létrehoz egy `Audits.json` az azonos kimenethez fájlt. Ha adatokat más jelentések, és a kimeneti formátum, amely nem kell megjegyzésbe a parancsfájl módosításával kísérletezhet.





## <a name="next-steps"></a>További lépések
* Szeretné testre szabhatja a mintákat a cikkben? Tekintse meg a [Azure Active Directory naplózási API-referencia](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Ha azt szeretné, hogy teljes áttekintése, az Azure Active Directory reporting API használatával [Bevezetés az Azure Active Directory reporting API használatába](active-directory-reporting-api-getting-started.md).
* Ha meg szeretne többet megtudni az Azure Active Directory reporting, tekintse meg a [Azure Active Directory-jelentéskészítés – útmutató](active-directory-reporting-guide.md).  

