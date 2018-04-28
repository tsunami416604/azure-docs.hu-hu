---
title: X.509 tanúsítvány létrehozása a PowerShell segítségével |} Microsoft Docs
description: PowerShell segítségével helyileg X.509-tanúsítvány létrehozása és engedélyezése az X.509-alapú biztonsági az az Azure IoT hub szimulált környezetben.
services: iot-hub
documentationcenter: ''
author: dsk-2015
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2017
ms.author: dkshir
ms.openlocfilehash: 2e58096d4bde9c947f199b4696c0b5c28291956d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>PowerShell-parancsfájlokkal kezelheti a hitelesítésszolgáltató által aláírt X.509-tanúsítványokat

Az IoT Hub X.509-alapú biztonsági ahhoz szükséges egy [X.509-tanúsítvány láncában](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), mely tartalmazza a legfelső szintű tanúsítvány, valamint a bármely beállítást a levéltanúsítvány köztes tanúsítványok. Ez *hogyan* az útmutató bemutatja, hogyan minta PowerShell-parancsfájlokat használó [OpenSSL](https://www.openssl.org/) létrehozására és aláírására X.509-tanúsítványokat. Azt javasoljuk, hogy ez az útmutató használata csak, kísérletezhet, mivel e lépések során gyártási folyamat a valós életben történik. Ezek a tanúsítványok segítségével biztonsági szimulálja az Azure IoT hub használatával a *X.509-tanúsítvány hitelesítése*. A jelen útmutató lépéseit tanúsítványok helyileg létrehozása a Windows-számítógépre. 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy, hogy rendelkezik érvényes módon kapott az OpenSSL bináris fájljait. Vagy esetleg
    - Töltse le a OpenSSL-forráskódot, és összeállítása a bináris fájlokat a számítógépre, vagy 
    - Töltse le és telepítse a [külső OpenSSL bináris](https://wiki.openssl.org/index.php/Binaries), például a [ebben a projektben a SourceForge](https://sourceforge.net/projects/openssl/).

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>X.509 tanúsítvány létrehozása
A következő lépések bemutatják az X.509 legfelső szintű tanúsítványok helyi létrehozása egy példát. 

1. Nyisson meg egy PowerShell-ablakot, egy *rendszergazda*.  
   **Megjegyzés:** meg kell nyitnia a PowerShell magát, nem PowerShell ISE, Visual Studio Code vagy más eszközök, amelyek az alapul szolgáló PowerShell-konzolban burkolása.  Nem-konzol használata alapján PowerShell eredményez `openssl` függő alábbi parancsokat.

2. Nyissa meg a munkakönyvtárat. Futtassa a következő a globális változókat. 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. Futtassa a következő parancsfájlt, amely az OpenSSL bináris fájljait másolja a munkakönyvtárat, és beállítja a környezeti változók:

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. Ezután futtassa az alábbi parancsfájlt, amely megkeresi az e tanúsítvány által a megadott *tulajdonosnévvel* már telepítve van, és hogy OpenSSL megfelelően van konfigurálva a számítógépen:
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    Ha minden megfelelően konfigurálva, megjelenik "Sikeres" üzenet. 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>X.509 tanúsítvány lánc létrehozása
Hozzon létre például egy tanúsítványlánc egy legfelső szintű hitelesítésszolgáltató, "CN = Azure IoT legfelső szintű hitelesítésszolgáltató", hogy ezt a mintát használja, a következő PowerShell-parancsfájl futtatásával. Ezt a parancsfájlt is frissíti a Windows operációs rendszer tanúsítványtárolójába, valamint hoz létre fájlokat a munkakönyvtárat. 
    1. Az alábbi parancsfájlt hoz létre egy PowerShell működnek, hozzon létre egy önaláírt tanúsítványt, egy adott *tulajdonosnévvel* és hatóság aláírása. 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. A következő PowerShell-függvény létrehozása köztes X.509-tanúsítványokat használ az előző függvény, valamint az OpenSSL bináris fájljait. 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. A következő PowerShell-függvény létrehozása az X.509-tanúsítvány láncában. Olvasási [láncok tanúsítvány](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) további információt.
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    Ez a parancsfájl létrehoz egy nevű fájlt *RootCA.cer* a a munkakönyvtárat. 
    4. Végül, használja a fenti PowerShell funkciók X.509 tanúsítványlánc létrehozásához futtassa a parancsot `New-CACertChain` a PowerShell-ablakban. 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>Az X.509 Hitelesítésszolgáltatói tanúsítvány birtokában igazolása

Ez a parancsfájl elvégzi a *igazolása a birtokában* az X.509 tanúsítvány folyamata. 

A PowerShell-ablakban az asztalon futtassa a következő kódot:
   
   ```PowerShell
   function New-CAVerificationCert([string]$requestedSubjectName)
   {
       $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
       $verifyRequestedFileName = ".\verifyCert4.cer"
       $rootCACert = Get-CACertBySubjectName $_rootCertSubject
       Write-Host "Using Signing Cert:::" 
       Write-Host $rootCACert
   
       $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

       Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
       if (-not (Test-Path $verifyRequestedFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
       }
   
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
   }
   New-CAVerificationCert "<your verification code>"
   ```

Ez a kód tanúsítványt hoz létre a megadott tulajdonosnévvel, nevű fájlba a hitelesítésszolgáltató által aláírt *VerifyCert4.cer* a a munkakönyvtárat. A tanúsítványfájl lemezeit az IoT hub, hogy rendelkezik-e a CA aláírási engedélye (Ez azt jelenti, hogy a titkos kulcs) segítségével.


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>Az eszköz levél X.509 tanúsítvány létrehozása

Ez a szakasz bemutatja egy PowerShell-parancsfájlt, amely létrehoz egy eszköz levéltanúsítvány és a megfelelő tanúsítványlánc használható. 

A PowerShell-ablakban a helyi számítógépen futtassa a következő parancsfájl egy hitelesítésszolgáltató által aláírt X.509 tanúsítvány, az eszköz létrehozásához:

   ```PowerShell
   function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
   {
       $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
       $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
       $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
       $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
       $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
   
       $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

       $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
   
       $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

       # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
       Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
       if (-not (Test-Path $newDevicePfxFileName))
       {
           throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
       }

       # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
       Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
       openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

       # Convert the PEM to get formats we can process
       if ($useEcc -eq $true)
       {
           openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       else
       {
           openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
       }
       openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
       Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
   }
   ```

Ezután futtassa `New-CADevice "<yourTestDevice>"` a PowerShell-ablakban, a rövid név, amely az eszköz létrehozásához használt használatával. Amikor a CA titkos kulcsok jelszavát kéri, adja meg "123". Ezzel létrehoz egy  _<yourTestDevice>.pfx_ fájlt a munkakönyvtárat.

## <a name="clean-up-certificates"></a>Tanúsítványok tisztítása

A start címsorában vagy **beállítások** app keresse meg és válassza ki **számítógép-tanúsítványok kezelése**. Távolítsa el a által kiállított összes tanúsítványban ** Azure IoT hitelesítésszolgáltató TestOnly x. Ezek a tanúsítványok léteznie kell a következő három helyen: 

* Tanúsítványok – helyi számítógép > személyes > tanúsítványok
* Tanúsítványok – helyi számítógép > megbízható legfelső szintű hitelesítésszolgáltatók > tanúsítványok
* Tanúsítványok – helyi számítógép > köztes hitelesítésszolgáltatók > tanúsítványok

   ![Távolítsa el az Azure IoT hitelesítésszolgáltató TestOnly](./media/iot-hub-security-x509-create-certificates/cleanup.png)
