---
title: Az Azure Active Directory jelentésnaplózási API-mintái | Microsoft Docs
description: Bevezetés az Azure Active Directory Reporting API használatába
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 345daaa8ac991cad9f098b317a9b59d52de73ead
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "41920687"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Az Azure Active Directory jelentésnaplózási API-mintái
Ez a cikk az Azure Active Directory Reporting API-ról szóló cikkgyűjtemény része.  
Az Azure AD Reporting egy olyan API-t biztosít, amely lehetővé teszi a naplózási adatokhoz való hozzáférést kóddal vagy valamilyen kapcsolódó eszközzel.
A cikk célja, hogy mintakódot biztosítson az **Audit API**-hoz.

Lásd:

* Az [auditnaplókkal](overview-reports.md#activity-reports) foglalkozó, további elméleti információkat tartalmazó részt
* További tudnivalók a Reporting API-ról: [Bevezetés az Azure Active Directory Reporting API használatába](concept-reporting-api.md)

Ha kérdése van, problémát tapasztal vagy visszajelzést szeretne küldeni, lépjen kapcsolatba az [AAD Reporting ügyfélszolgálatával](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Előfeltételek
Mielőtt használhatná a cikkben szereplő mintákat, meg kell felelnie az [előfeltételeknek, hogy hozzá tudjon férni az Azure AD Reporting API-hoz](howto-configure-prerequisites-for-reporting-api.md).  

## <a name="known-issue"></a>Ismert probléma
Az alkalmazáshitelesítés nem működik, ha az Ön bérlője az európai régióban van. Áthidaló megoldásként használjon felhasználói hitelesítést az Audit API eléréséhez, amíg fel nem számoljuk a problémát. 

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

### <a name="executing-the-powershell-script"></a>A PowerShell-szkript végrehajtása
Miután befejezte a szkript szerkesztését, futtassa és ellenőrizze, hogy a rendszer a várt adatokat adja vissza az auditnapló-jelentésekből.

A szkript a naplójelentésből származó kimenetet JSON formátumban adja vissza. Egy `Audits.json` fájlt is létrehoz, ugyanazzal a kimenettel. Kísérletezhet a szkriptek módosításával, hogy más jelentésekből származó adatokat adjanak vissza, illetve tegyék megjegyzésbe azokat a kimeneti formátumokat, amelyekre nincs szüksége.





## <a name="next-steps"></a>További lépések
* Szeretné testre szabni a cikkben szereplő mintákat? Tekintse át az [Azure Active Directory Audit API referenciáit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit). 
* Ha át szeretné tanulmányozni az Azure Active Directory Reporting API használatáról szóló teljes áttekintést, nézzen bele az [Azure Active Directory Reporting API használatába bevezető](concept-reporting-api.md) cikkbe.
* Ha többet szeretne megtudni az Azure Active Directory Reportingról, tekintse át az [Azure Active Directory Reporting útmutatóját](overview-reports.md).  

