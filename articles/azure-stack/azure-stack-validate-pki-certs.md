---
title: Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványok integrált Azure verem rendszerek telepítés ellenőrzéséhez |} Microsoft Docs
description: Az Azure verem PKI-tanúsítványok integrált Azure verem rendszerekhez ellenőrzését ismerteti. Ismerteti az Azure verem tanúsítvány ellenőrző eszközének használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605610"
---
# <a name="validate-azure-stack-pki-certificates"></a>Azure verem PKI-tanúsítványok ellenőrzése

A jelen cikkben ismertetett Azure verem készültségi ellenőrző eszköz [a PowerShell-galériából](https://aka.ms/AzsReadinessChecker). Az eszköz segítségével ellenőrizze, hogy a [generált PKI-tanúsítványok](azure-stack-get-pki-certs.md) alkalmasak a központi telepítés előtti. Tesztelje, majd újból kiadja azokat tanúsítványokat, szükség esetén elegendő időt távozó ellenőriznie kell a tanúsítványokat.

A készenléti-ellenőrző eszközt hajtja végre a következő tanúsítvány ellenőrzések:

- **Olvassa el a PFX**  
    Érvényes PFX-fájl helyes jelszót keres, és figyelmeztet, ha nyilvános információ nem védi jelszó. 
- **Aláírási algoritmus**  
    Ellenőrzi, hogy az aláírási algoritmus nem SHA1.
- **Titkos kulcs**  
    Ellenőrzi, hogy a titkos kulcs létezik-e, és exportálja a helyi számítógép attribútummal. 
- **Tanúsítványlánc**  
    Ellenőrzések tanúsítványlánc érintetlen is, hogy önaláírt tanúsítványokat.
- **DNS-nevek**  
    A SAN tartalmaz minden végpontra vonatkozó DNS-neveit, vagy egy támogatása jelen-e helyettesítő ellenőrzi.
- **Kulcshasználat**  
    Ellenőrzi, ha a kulcshasználat a digitális aláírás és kulcstitkosítás tartalmaz, és a kibővített kulcshasználathoz tartalmazza a kiszolgáló hitelesítése és ügyfél-hitelesítéshez.
- **Kulcs mérete**  
    Ellenőrzi, hogy a kulcs mérete 2048 vagy nagyobb.
- **Lánc sorrendje**  
    Ellenőrzi az ellenőrzése, hogy helyes-e a sorrendet, az egyéb tanúsítványok sorrendjét.
- **Más tanúsítványok**  
    Gondoskodjon arról, hogy nincs más tanúsítványok van csomagolva, PFX nem a megfelelő levéltanúsítvány vagy láncában.
- **Nincs profil**  
    Ellenőrzi, hogy egy új felhasználó tudja betölteni a PFX-adatainak nélkül a felhasználói profil betöltése, a csoportosan felügyelt szolgáltatásfiók fiókok viselkedését mimicking tanúsítvány karbantartás során.

> [!IMPORTANT]  
> A PKI-tanúsítványt egy PFX-fájlt, és jelszót kell tekinteni a bizalmas adatokat.

## <a name="prerequisites"></a>Előfeltételek

A rendszer meg kell felelnie a következő előfeltételek PKI-tanúsítványok telepítését bemutató Azure verem érvényesítése előtt:

- A Microsoft Azure verem készültségének ellenőrzése
- A következő exportált SSL tanúsítvány(ok) a [utasítások előkészítése](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 vagy Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Hajtsa végre az alapvető szolgáltatások tanúsítvány érvényesítése

Előkészítéséhez és az Azure verem PKI-tanúsítványok telepítésének és a titkos Elforgatás érvényesítéséhez, tegye a következőket:

1. Telepítés **AzsReadinessChecker** egy PowerShell parancssorból (5.1-es vagy újabb), a következő parancsmagot:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. A tanúsítvány könyvtárstruktúrát létrehozása. Az alábbi példában a módosíthatja `<c:\certificates>` számára az Ön által választott az új elérési utat.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > Az AD FS és a Graph szükségesek, ha az azonosítási rendszer AD FS használ.
    
     - A megfelelő könyvtár az előző lépésben létrehozott helyezze el az (oka) t. Példa:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. A PowerShell-ablakot, módosítsa az értékeket **RegionName** és **FQDN** az Azure-verem környezetben megfelelő, és futtassa a következő parancsot:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. Ellenőrizze, hogy a kimeneti és az összes tanúsítvány összes tesztjét. Példa:

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>Ismert problémák

**Jelenség**: kimarad a tesztek

**OK**: AzsReadinessChecker kihagyja bizonyos teszteket, ha egy függőség nem teljesül:

 - Más tanúsítványok kimarad a tanúsítványlánc meghibásodásakor.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**Megoldási**: kövesse az eszköz útmutató alapján minden egyes tanúsítvány teszteket hajtson végre a Részletek területen.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Egy szolgáltatás tanúsítvány érvényességi végrehajtásához platform

Használja ezeket a lépéseket az Azure verem PKI-tanúsítványok platform ellenőrzése egy platformszolgáltatási tanúsítványokként Ha SQL/MySQL vagy alkalmazásszolgáltatások telepítések tervbe van véve.

1.  Telepítés **AzsReadinessChecker** egy PowerShell parancssorból (5.1-es vagy újabb), a következő parancsmagot:

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  Hozzon létre egy beágyazott kivonattáblát tartalmazó elérési útja és a jelszó érvényesítése kellene PaaS tanúsítványokra. A PowerShell-ablakban futtassa:

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Értékeit **RegionName** és **FQDN** Azure verem környezetét az érvényesítés megkezdéséhez kereséséhez. Ezután futtassa:

    ```PowerShell
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Ellenőrizze, hogy a kimenet, és hogy összes tanúsítvány összes tesztjét.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>Ellenőrzött tanúsítványok használatával

Ha a tanúsítványok érvényesítése a AzsReadinessChecker által készen áll is használhatja őket az Azure Alkalmazásveremben üzembe, vagy Azure verem titkos elforgatási. 

 - Üzembe helyezés esetén biztonságos átvitelére a tanúsítványok a központi telepítés szakértőnek, hogy a megadott központi telepítési gazdagép-kiszolgálóra átmásolhatók a [Azure verem nyilvános kulcsokra épülő infrastruktúra követelményei dokumentáció](azure-stack-pki-certs.md).
 - Titkos elforgatás, használhatja a tanúsítványok következő Azure verem környezetét nyilvános infrastruktúra végpontok régi tanúsítványok frissítése a [Azure verem titkos Elforgatás dokumentáció](azure-stack-rotate-secrets.md).
 - A PaaS-szolgáltatásokra, SQL, a MySQL és az App Services erőforrás-szolgáltatók telepítéséhez Azure verem követve a tanúsítványokat is használhat a [áttekintése Azure verem dokumentációjának szolgáltatásokat kínáló](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>További lépések

[Datacenter identitásintegráció](azure-stack-integrate-identity.md)