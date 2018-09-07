---
title: Az Azure Stackkel integrált rendszerek üzembe helyezés az Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványainak előállításához |} A Microsoft Docs
description: Azure Stack integrált rendszerek Azure Stack PKI tanúsítvány üzembe helyezési folyamatát ismerteti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 698e044aea6bbd78847cb209160c1fa6b2edcdbf
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44023419"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Az Azure Stack-tanúsítványok aláíró kérés létrehozása

A jelen cikkben ismertetett Azure Stack készültségi ellenőrző eszköz [a PowerShell-galériából](https://aka.ms/AzsReadinessChecker). Az eszköz létrehoz egy Azure Stack üzembe helyezéshez megfelelő tanúsítvány aláírási kérelem (tanúsítvány-aláírási kérelmeinek). Tanúsítványokat kell kért, jönnek létre és ellenőrzi a telepítés előtt tesztelje ideje.

Az Azure Stack készültségi ellenőrző eszköz (AzsReadinessChecker) hajtja végre a következő eszköztanúsítvány-igénylések:

 - **Standard szintű Eszköztanúsítvány-igénylések**  
    A következők szerint kérelem [PKI-tanúsítványok létrehozása az Azure Stack üzembe helyezéséhez](azure-stack-get-pki-certs.md).
 - **Platform-– szolgáltatásként**  
    Szükség esetén a platform--szolgáltatásként (PaaS) nevek a megadott tanúsítványok kérése [Azure Stack a nyilvános kulcsú infrastruktúra tanúsítványkövetelmények – nem kötelező PaaS-tanúsítványok](azure-stack-pki-certs.md#optional-paas-certificates).



## <a name="prerequisites"></a>Előfeltételek

A rendszer a PKI-tanúsítványai az Azure Stack üzemelő példányához tartozó CSR(s) létrehozása előtt kell felelnie a következő előfeltételek vonatkoznak:

 - A Microsoft Azure Stack készültség ellenőrzése
 - Tanúsítvány attribútumai:
    - Régió neve
    - Külső, teljesen minősített tartománynevét (FQDN)
    - Tárgy
 - Windows 10 vagy Windows Server 2016
 
  > [!NOTE]
  > Ha megkapta a tanúsítványok biztonsági a hitelesítésszolgáltatótól származó lépéseit [előkészítése az Azure Stack PKI-tanúsítványokat](azure-stack-prepare-pki-certs.md) hajtható végre, ugyanazon a rendszeren kell!

## <a name="generate-certificate-signing-requests"></a>A tanúsítvány-aláírási kérelem (kérelmek) létrehozása

Ezen lépések előkészítéséhez és az Azure Stack PKI-tanúsítványok ellenőrzése: 

1.  Telepítse a AzsReadinessChecker PowerShell parancssorból (5.1-es vagy újabb), a következő parancsmagot:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  Deklarálja a **tulajdonos** , egy rendezett szótárban. Példa: 

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > Ha egy köznapi név (CN) meg van adva ez felülírja az első DNS-nevét a tanúsítványkérelemben.

3.  Deklaráljon egy már létező kimeneti könyvtárat. Példa:

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  Deklarálja rendszer azonosítása

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Active Directory összevonási szolgáltatások

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Deklarálja **régió neve** és a egy **külső FQDN** szánt az Azure Stack üzemelő példányához.

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` alapjául szolgál, amelyen az összes külső DNS-neveket az Azure Stackben jönnek létre, ebben a példában, a portál `portal.east.azurestack.contoso.com`.  

6. Tanúsítvány-aláírási kérelem minden DNS-név generálása:

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Tartalmazza a PaaS-szolgáltatások az kapcsoló megadása ```-IncludePaaS```

7. Másik lehetőségként a fejlesztési és tesztelési környezeteket. Létrehozásához több tulajdonos alternatív nevekkel egyetlen tanúsítványkérelem hozzáadása **- RequestType SingleCSR** paraméter és érték (**nem** az éles környezetekhez ajánlott):

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    Tartalmazza a PaaS-szolgáltatások az kapcsoló megadása ```-IncludePaaS```
    
8. Tekintse át a kimenetet:

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  Küldje el a **. KÉRÉS** fájlt hoz létre, a hitelesítésszolgáltató (belső vagy nyilvános).  A kimeneti könyvtárba, **Start-AzsReadinessChecker** egy hitelesítésszolgáltatónak elküldéséhez szükséges CSR(s) tartalmazza.  A gyermek könyvtárra, amelyben a kérés generálásakor, referenciaként használt INF-fájlok is tartalmaz. Győződjön meg, hogy a a hitelesítésszolgáltató tanúsítványokat a generált kérést a megfelelő állít elő a [Azure Stack nyilvános kulcsokra épülő infrastruktúra követelményei](azure-stack-pki-certs.md).

## <a name="next-steps"></a>További lépések

[Készítse elő az Azure Stack PKI-tanúsítványok](azure-stack-prepare-pki-certs.md)
