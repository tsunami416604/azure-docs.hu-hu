---
title: Tanúsítványok létrehozása Microsoft Azure Stack hub Readiness-ellenőrző eszköz használatával | Microsoft Docs
description: Útmutatás a tanúsítványkérelmek létrehozásához, majd a tanúsítványok lekéréséhez és telepítéséhez a Azure Stack Edge GPU-eszközön az Azure Stack hub Readiness-ellenőrző eszköz használatával.
services: Azure Stack Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: ea560cf7d61a208cf29aef977aea6d1584a7cdb7
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146046"
---
# <a name="create-certificates-for-your-azure-stack-edge-using-azure-stack-hub-readiness-checker-tool"></a>Tanúsítványok létrehozása az Azure Stack Edge-hez Azure Stack hub Readiness-ellenőrző eszköz használatával 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez a cikk bemutatja, hogyan hozhat létre tanúsítványokat az Azure Stack Edge-hez az Azure Stack hub Readiness-ellenőrző eszközével. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack hub Readiness-ellenőrző eszköz használata

Az Azure Stack hub Readiness-ellenőrző eszköz használatával tanúsítvány-aláírási kérelmeket (munkatársakat) hozhat létre egy Azure Stack Edge-eszköz telepítéséhez. Ezeket a kérelmeket a Azure Stack Edge-eszközre vonatkozó megrendelés elhelyezése után hozhatja létre, és megvárja az eszköz érkezését. 

> [!NOTE]
> Ezt az eszközt csak tesztelési vagy fejlesztési célokra, illetve éles eszközökre nem használhatja. 

Az Azure Stack hub Readiness-ellenőrző eszköz (AzsReadinessChecker) használatával a következő tanúsítványokat kérheti le:

- Tanúsítvány Azure Resource Manager
- Helyi felhasználói felületi tanúsítvány
- Csomópont-tanúsítvány
- BLOB-tanúsítvány
- VPN-tanúsítvány


## <a name="prerequisites"></a>Előfeltételek

Ha az ügyfélszolgálatot Azure Stack peremhálózati eszköz telepítéséhez szeretné létrehozni, győződjön meg a következőket: 

- Windows 10 vagy Windows Server 2016 vagy újabb rendszert futtató ügyfél. 
- Letöltötte az Microsoft Azure Stack hub Readiness-ellenőrző eszköz 1.2002.1133.85 a rendszer [PowerShell-Galéria](https://aka.ms/AzsReadinessChecker) . Előfordulhat, hogy keresnie kell erre a csomagra. Csak az eszköz ezen verziója hozhat létre tanúsítványokat Azure Stack peremhálózati eszközökhöz.
- A tanúsítványokkal kapcsolatban a következő információk szerepelnek:
  - Eszköz neve
  - Csomópont sorozatszáma
  - Külső teljesen minősített tartománynév (FQDN)

## <a name="generate-certificate-signing-requests"></a>Tanúsítvány-aláírási kérelmek előállítása

Az Azure Stack Edge-eszközök tanúsítványainak előkészítéséhez használja az alábbi lépéseket:

1. Futtassa a PowerShellt rendszergazdaként (5,1 vagy újabb).
2. Telepítse az Azure Stack hub Readiness-ellenőrző eszközt. A PowerShell parancssorába írja be a következőt: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker -RequiredVersion 1.2002.1133.85 -Force
    ```

    A telepített verzió ellenőrzéséhez írja be a következőt:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Hozzon létre egy könyvtárat az összes tanúsítványhoz, ha az nem létezik. Típus: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Tanúsítványkérelem létrehozásához adja meg a következő információkat. Ha VPN-tanúsítványt hoz létre, néhány ilyen bemenet nem érvényes. 
    
    |Input (Bemenet) |Leírás  |
    |---------|---------|
    |`OutputRequestPath`|A fájl elérési útja a helyi ügyfélszámítógépen, amelyen létre szeretné hozni a tanúsítványokat.        |
    |`DeviceName`|Az eszköz helyi webes felhasználói felületének **eszközök** lapján található név. <br> Ez a mező nem szükséges a VPN-tanúsítványokhoz.         |
    |`NodeSerialNumber`|Az eszköz csomópontjának sorozatszáma a **hálózat** lapon az eszköz helyi webes felületén. <br> Ez a mező nem szükséges a VPN-tanúsítványokhoz.       |
    |`ExternalFQDN`|Az eszköz helyi webes FELÜLETének **eszközök** lapján található DNSDomain érték.         |
    |`RequestType`|A kérelem típusa a `MultipleCSR` különböző végpontokhoz tartozó tanúsítványok esetében lehet, vagy `SingleCSR` – egyetlen tanúsítvány az összes végponthoz. <br> Ez a mező nem szükséges a VPN-tanúsítványokhoz.     |

    A VPN-tanúsítvány kivételével az összes tanúsítvány esetében írja be a következőt: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Ha VPN-tanúsítványt hoz létre, írja be a következőt: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. A tanúsítványkérelem fájljait a fenti OutputRequestPath paraméterben megadott könyvtárba fogja megtalálni. A paraméter használatakor `MultipleCSR` a rendszer 4 fájlt fog látni a `.req` kiterjesztéssel. A fájlok a következők:

    
    |Fájlnevek  |Tanúsítványkérelem típusa  |
    |---------|---------|
    |Kezdve a `DeviceName`     |Helyi webes FELHASZNÁLÓIFELÜLET-tanúsítvány kérése      |
    |Kezdve a `NodeSerialNumber`     |Csomópont-tanúsítványkérelem         |
    |Kezdés `login`     |Azure Resource Manager Endpoint Certificate kérelem       |
    |Kezdés `wildcard`     |BLOB Storage-tanúsítvány kérése; helyettesítő karaktert tartalmaz, mert az az eszközön létrehozott összes Storage-fiókot tartalmazza.          |
    |Kezdés `AzureStackEdgeVPNCertificate`     |VPN-ügyféltanúsítvány iránti kérelem.         |

    Megjelenik egy INF-mappa is. Ez egy felügyeleti. <Edge-DeviceName> információs fájlt tartalmaz a tanúsítvány részleteit magyarázó szövegben.  


6. Küldje el ezeket a fájlokat a hitelesítésszolgáltatótól (belső vagy nyilvános). Győződjön meg arról, hogy a HITELESÍTÉSSZOLGÁLTATÓ olyan tanúsítványokat hoz létre a generált kérelem alapján, amelyek megfelelnek a [csomópont-tanúsítványok](azure-stack-edge-j-series-manage-certificates.md#node-certificates), a [végponti tanúsítványok](azure-stack-edge-j-series-manage-certificates.md#endpoint-certificates)és a [helyi felhasználói felületi tanúsítványok](azure-stack-edge-j-series-manage-certificates.md#local-ui-certificates)Azure stack peremhálózati tanúsítványokra vonatkozó követelményeinek.

## <a name="prepare-certificates-for-deployment"></a>Tanúsítványok előkészítése központi telepítéshez

A HITELESÍTÉSSZOLGÁLTATÓTÓL beszerzett tanúsítványfájl-fájlokat importálni és exportálni kell olyan tulajdonságokkal, amelyek megfelelnek Azure Stack peremhálózati eszköz tanúsítványára vonatkozó követelményeknek. Hajtsa végre a következő lépéseket ugyanazon a rendszeren, amelyen a tanúsítvány-aláírási kéréseket létrehozta.

- A tanúsítványok importálásához kövesse a [tanúsítványok importálása az Azure stack Edge-eszközt elérő ügyfeleken](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)című témakör lépéseit.

- A tanúsítványok exportálásához kövesse az [Azure stack Edge-eszközt elérő ügyfél tanúsítványának exportálása](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)című témakör lépéseit.


## <a name="validate-certificates"></a>Tanúsítványok ellenőrzése

Először létrehoz egy megfelelő mappastruktúrát, és elhelyezi a tanúsítványokat a megfelelő mappákban. Csak ezután érvényesíti a tanúsítványokat az eszköz használatával.

1. Futtassa a PowerShellt rendszergazdaként.

2. A mappa megfelelő struktúrájának létrehozásához a parancssorba írja be a következőt:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. A PFX-jelszó konvertálása biztonságos karakterlánccá. Típus:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Ezután érvényesítse a tanúsítványokat. Típus:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Következő lépések

[Az Azure Stack Edge-eszköz üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md)
